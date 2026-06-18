# Módulo 3 — Dependencia espacial en flujos OD

## Por qué los flujos tienen dependencia espacial

Los flujos entre nodos de una red no son independientes:
- Orígenes vecinos compiten por los mismos destinos (dependencia en la dimensión origen).
- Destinos vecinos compiten por los mismos orígenes (dependencia en la dimensión destino).
- Los flujos entre nodos cercanos son más similares que entre nodos lejanos.

Si los residuos del modelo OD muestran autocorrelación, los SE son ineficientes
y la inferencia es inválida.

## Construcción de la W para redes OD

```python
import numpy as np
from libpysal.weights import W

def w_desde_lista(vecindades: dict) -> W:
    """
    vecindades: {id_unidad: [ids_vecinos], ...}
    """
    return W(vecindades, silence_warnings=True)


def w_knn_nodos(gdf, k: int = 5):
    """
    W de k vecinos más cercanos para los nodos (orígenes O destinos).
    """
    from libpysal.weights import KNN
    return KNN.from_dataframe(gdf, k=k, silence_warnings=True)


def residuos_por_origen(
    df_od: pd.DataFrame,
    col_residuo: str,
    col_origen: str
) -> pd.DataFrame:
    """
    Agrega residuos del modelo OD por origen (media de residuos de cada i).
    Permite calcular Moran en la dimensión de origen.
    """
    return df_od.groupby(col_origen)[col_residuo].mean().reset_index()


def residuos_por_destino(
    df_od: pd.DataFrame,
    col_residuo: str,
    col_destino: str
) -> pd.DataFrame:
    """Análogo por destino."""
    return df_od.groupby(col_destino)[col_residuo].mean().reset_index()
```

## Test de Moran en dimensión origen y destino

```python
import pandas as pd
from esda.moran import Moran
import geopandas as gpd

def moran_od(
    df_od: pd.DataFrame,
    gdf_nodos: gpd.GeoDataFrame,
    col_residuo: str,
    col_origen: str,
    col_destino: str,
    col_id_nodo: str,
    k: int = 5,
    permutaciones: int = 999
) -> dict:
    """
    Calcula Moran's I de los residuos agregados por origen y por destino.
    """
    from libpysal.weights import KNN

    w = KNN.from_dataframe(gdf_nodos, k=k, silence_warnings=True)
    w.transform = "r"

    # Dimensión origen
    res_orig = residuos_por_origen(df_od, col_residuo, col_origen)
    res_orig = res_orig.set_index(col_origen).reindex(gdf_nodos[col_id_nodo].values)[col_residuo]
    moran_orig = Moran(res_orig.fillna(0).values, w, permutations=permutaciones)

    # Dimensión destino
    res_dest = residuos_por_destino(df_od, col_residuo, col_destino)
    res_dest = res_dest.set_index(col_destino).reindex(gdf_nodos[col_id_nodo].values)[col_residuo]
    moran_dest = Moran(res_dest.fillna(0).values, w, permutations=permutaciones)

    print(f"Moran I (residuos por ORIGEN)  = {moran_orig.I:.4f}, p = {moran_orig.p_sim:.4f}")
    print(f"Moran I (residuos por DESTINO) = {moran_dest.I:.4f}, p = {moran_dest.p_sim:.4f}")

    if moran_orig.p_sim < 0.05 or moran_dest.p_sim < 0.05:
        print("DIAGNÓSTICO: dependencia espacial en residuos. Aplicar corrección "
              "(efectos fijos adicionales o filtrado espectral — ver modulo4-eigenfunction.md).")

    return {
        "moran_origen": moran_orig,
        "moran_destino": moran_dest
    }
```

## Correcciones disponibles

| Situación | Corrección recomendada |
|-----------|------------------------|
| Solo dependencia en origen | Efectos fijos de origen (ya deberían estar) + eigenvectores W_origen |
| Solo dependencia en destino | Efectos fijos de destino + eigenvectores W_destino |
| Dependencia en ambas | Eigenvectores de W_origen Y W_destino |
| Dependencia en el flujo T_ij | Modelo SAC para flujos (complejo; ver Fischer & Wang Ch. 5) |

## Autocorrelación de autoregresión en flujos (OD-SAR)

Para modelar dependencia directa en los flujos (no solo en los nodos):

```python
def od_sar_weights(n_origins: int, n_dests: int, w_orig_arr: np.ndarray) -> np.ndarray:
    """
    Construye la matriz de pesos para el modelo OD-SAR (LeSage & Pace 2008).
    El bloque diagonal de la W de flujos está dado por la W de orígenes.
    Solo para datasets pequeños (n < 200 nodos).
    """
    # Cada origen i comparte su vecindad con todos los destinos
    W_od = np.kron(w_orig_arr, np.ones((n_dests, n_dests)))
    # Normalizar por filas
    row_sums = W_od.sum(axis=1, keepdims=True)
    row_sums[row_sums == 0] = 1
    return W_od / row_sums
```

Advertencia: la W para flujos OD tiene dimensión `(n·m) × (n·m)` — con 100 orígenes
y 100 destinos la matriz es 10,000 × 10,000. Solo viable para redes pequeñas.
Para redes grandes, preferir filtrado espectral (modulo4-eigenfunction.md).
