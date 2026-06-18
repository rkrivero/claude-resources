# Módulo 1 — Cubo espacio-temporal y MTUP

## Estructura del cubo

El cubo organiza datos en 3 dimensiones: `(unidad_espacial × período × variable)`.
Para n unidades y t períodos, el cubo tiene n × t celdas.

```python
import pandas as pd
import geopandas as gpd
import numpy as np
from typing import Callable

def construir_cubo(
    gdf_base: gpd.GeoDataFrame,
    datos_temporales: pd.DataFrame,
    col_id: str,
    col_periodo: str,
    col_valor: str
) -> gpd.GeoDataFrame:
    """
    Construye el cubo uniendo geometrías (gdf_base) con los datos temporales.
    gdf_base: GeoDataFrame de unidades espaciales (una fila por unidad).
    datos_temporales: DataFrame largo con col_id, col_periodo, col_valor.
    """
    cubo = gdf_base[[col_id, "geometry"]].merge(
        datos_temporales[[col_id, col_periodo, col_valor]],
        on=col_id,
        how="inner"
    )
    return cubo


def pivot_cubo(
    cubo: gpd.GeoDataFrame,
    col_id: str,
    col_periodo: str,
    col_valor: str
) -> pd.DataFrame:
    """
    Transforma el cubo largo en matriz (n_unidades × n_periodos) para análisis temporal.
    """
    return cubo.pivot_table(
        index=col_id, columns=col_periodo, values=col_valor, aggfunc="first"
    ).sort_index(axis=1)


def verificar_cubo(df_pivot: pd.DataFrame) -> None:
    """Reporta completitud del cubo."""
    n_unidades, n_periodos = df_pivot.shape
    n_celdas = n_unidades * n_periodos
    n_missing = df_pivot.isna().sum().sum()
    print(f"Cubo: {n_unidades} unidades × {n_periodos} períodos = {n_celdas} celdas")
    print(f"Celdas vacías: {n_missing} ({n_missing/n_celdas*100:.1f}%)")
    if n_missing / n_celdas > 0.1:
        print("ADVERTENCIA: > 10% de celdas vacías. Considerar imputación o filtrado.")
    if n_periodos < 10:
        print("ADVERTENCIA: < 10 períodos. Mann-Kendall tendrá baja potencia estadística.")
```

## MTUP — Modifiable Temporal Unit Problem

Análogo temporal del MAUP: los resultados cambian según la granularidad del período.

```python
def sensibilidad_mtup(
    datos_brutos: pd.DataFrame,
    col_id: str,
    col_fecha: str,
    col_valor: str,
    frecuencias: list[str] = ["ME", "QE", "YE"],  # mensual, trimestral, anual
    funcion_agg: Callable = np.mean
) -> dict[str, pd.DataFrame]:
    """
    Evalúa la sensibilidad del cubo a distintas granularidades temporales.
    Devuelve diccionario {frecuencia: pivot_cubo}.
    """
    datos_brutos = datos_brutos.copy()
    datos_brutos[col_fecha] = pd.to_datetime(datos_brutos[col_fecha])
    datos_brutos = datos_brutos.set_index(col_fecha)

    resultados = {}
    for freq in frecuencias:
        agg = (datos_brutos.groupby([col_id, pd.Grouper(freq=freq)])[col_valor]
               .agg(funcion_agg)
               .reset_index())
        pivot = agg.pivot(index=col_id, columns=col_fecha, values=col_valor)
        resultados[freq] = pivot
        print(f"Frecuencia {freq}: {pivot.shape[1]} períodos")

    return resultados


def reportar_mtup(cubos: dict[str, pd.DataFrame]) -> None:
    """
    Compara la estabilidad de la media espacial entre granularidades.
    Si el ranking de unidades cambia drásticamente, reportar inestabilidad temporal.
    """
    medias = {freq: df.mean(axis=1) for freq, df in cubos.items()}
    from scipy.stats import spearmanr
    freqs = list(medias.keys())
    print("\nCorrelaciones de Spearman entre granularidades (ranking de medias):")
    for i in range(len(freqs)):
        for j in range(i+1, len(freqs)):
            r, p = spearmanr(medias[freqs[i]], medias[freqs[j]])
            estado = "estable" if r > 0.8 else "INESTABLE — reportar"
            print(f"  {freqs[i]} vs {freqs[j]}: ρ = {r:.3f}, p = {p:.4f} → {estado}")
```

## Umbral mínimo de períodos

| Períodos | Análisis recomendable |
|----------|----------------------|
| < 6      | Solo comparación estática entre 2-3 cortes temporales; NO tendencias |
| 6 – 9    | Mann-Kendall con potencia limitada; reportar como exploratorio |
| ≥ 10     | Análisis completo de 17 categorías con confianza |
| ≥ 20     | Permite detectar oscilaciones y ciclos confiablemente |

## Imputación de celdas vacías

Si el cubo tiene huecos (< 10% de celdas):

```python
def imputar_cubo(df_pivot: pd.DataFrame, metodo: str = "interpolate") -> pd.DataFrame:
    """
    metodo: 'interpolate' (interpolación lineal por unidad) |
            'forward' (último valor conocido) |
            'mean' (media de la unidad)
    """
    if metodo == "interpolate":
        return df_pivot.interpolate(axis=1, method="linear", limit_direction="both")
    elif metodo == "forward":
        return df_pivot.fillna(method="ffill", axis=1)
    elif metodo == "mean":
        return df_pivot.apply(lambda row: row.fillna(row.mean()), axis=1)
    raise ValueError(f"Método desconocido: {metodo}")
```

Siempre reportar qué imputación se usó y cuántas celdas afectó.
