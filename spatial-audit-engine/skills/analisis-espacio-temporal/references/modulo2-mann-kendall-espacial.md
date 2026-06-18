# Módulo 2 — Mann-Kendall espacial por celda del cubo

El test de Mann-Kendall (MK) detecta tendencias monótonas en series temporales
sin asumir normalidad de los datos. Aplicado celda a celda en el cubo, produce
un campo de tendencias espacialmente distribuido.

## Test de Mann-Kendall por unidad

```python
import numpy as np
import pandas as pd
from scipy.stats import kendalltau

def mann_kendall_serie(serie: np.ndarray) -> dict:
    """
    Test de Mann-Kendall para una única serie temporal.
    Devuelve: tau, p-valor, sentido de la tendencia.
    H0: no hay tendencia monótona.
    """
    t_arr = np.arange(len(serie))
    # Eliminar NaN
    mask = ~np.isnan(serie)
    if mask.sum() < 6:
        return {"tau": np.nan, "p": np.nan, "tendencia": "insuficiente"}
    tau, p = kendalltau(t_arr[mask], serie[mask])
    sentido = "creciente" if tau > 0 else "decreciente" if tau < 0 else "plana"
    return {"tau": tau, "p": p, "tendencia": sentido}


def mk_espacial(
    df_pivot: pd.DataFrame,
    alpha: float = 0.05
) -> pd.DataFrame:
    """
    Aplica Mann-Kendall a cada fila (unidad espacial) del cubo pivot.
    df_pivot: índice = unidades, columnas = períodos ordenados cronológicamente.
    Devuelve DataFrame con: tau, p, tendencia, significativo.
    """
    resultados = df_pivot.apply(
        lambda row: pd.Series(mann_kendall_serie(row.values)), axis=1
    )
    resultados["significativo"] = resultados["p"] < alpha
    n_sig = resultados["significativo"].sum()
    n_total = len(resultados)
    print(f"Mann-Kendall (α={alpha}): {n_sig}/{n_total} unidades con tendencia significativa "
          f"({n_sig/n_total*100:.1f}%)")
    return resultados
```

## Corrección por comparaciones múltiples

Con n unidades, se realizan n tests simultáneos: ~5% pueden ser falsos positivos.
Aplicar FDR (Benjamini-Hochberg) antes de clasificar categorías de hot spot:

```python
from statsmodels.stats.multitest import multipletests

def corregir_mk(mk_resultados: pd.DataFrame, metodo: str = "fdr_bh") -> pd.DataFrame:
    """
    metodo: 'fdr_bh' (Benjamini-Hochberg, recomendado) |
            'bonferroni' (más conservador)
    """
    mask_validos = mk_resultados["p"].notna()
    p_vals = mk_resultados.loc[mask_validos, "p"].values
    reject, p_adj, _, _ = multipletests(p_vals, method=metodo)
    mk_resultados = mk_resultados.copy()
    mk_resultados.loc[mask_validos, "p_adj"] = p_adj
    mk_resultados.loc[mask_validos, "significativo_adj"] = reject
    n_antes = mk_resultados["significativo"].sum()
    n_despues = reject.sum()
    print(f"Corrección {metodo}: {n_antes} → {n_despues} unidades significativas")
    return mk_resultados
```

## Visualización del campo de tendencias

```python
import geopandas as gpd
import matplotlib.pyplot as plt
from matplotlib.colors import TwoSlopeNorm

def mapa_tendencias_mk(
    gdf: gpd.GeoDataFrame,
    mk_resultados: pd.DataFrame,
    col_id: str
) -> None:
    """
    Mapa coropleta de tau (tendencia) con:
    - Color: τ (divergente, anclado en 0)
    - Unidades no significativas: gris (sin color temático)
    """
    gdf_plot = gdf.merge(mk_resultados, left_on=col_id, right_index=True, how="left")

    # Máscara: solo pintar unidades con tendencia significativa
    gdf_plot["tau_masked"] = np.where(
        gdf_plot.get("significativo_adj", gdf_plot["significativo"]),
        gdf_plot["tau"],
        np.nan
    )

    fig, ax = plt.subplots(figsize=(10, 8))
    # Fondo gris para no-significativos
    gdf_plot.plot(color="#e0e0e0", ax=ax, linewidth=0.3, edgecolor="white")
    # Tendencias significativas con paleta divergente
    tau_max = gdf_plot["tau_masked"].abs().max()
    norm = TwoSlopeNorm(vcenter=0, vmin=-tau_max, vmax=tau_max)
    gdf_plot.dropna(subset=["tau_masked"]).plot(
        column="tau_masked", cmap="RdBu_r", norm=norm,
        ax=ax, linewidth=0.3, edgecolor="white", legend=True,
        legend_kwds={"label": "τ Mann-Kendall", "shrink": 0.6}
    )
    ax.set_title("Tendencias temporales por unidad espacial (Mann-Kendall)")
    ax.axis("off")
    plt.tight_layout()
```

## Interpretación de τ (tau de Kendall)

| τ | Interpretación |
|---|----------------|
| +0.7 a +1.0 | Tendencia creciente muy fuerte y consistente |
| +0.3 a +0.7 | Tendencia creciente moderada |
| −0.3 a +0.3 | Sin tendencia clara (o tendencia débil) |
| −0.7 a −0.3 | Tendencia decreciente moderada |
| −1.0 a −0.7 | Tendencia decreciente muy fuerte y consistente |

## Limitaciones

- MK asume independencia temporal de las observaciones. Si la serie tiene
  autocorrelación temporal (datos mensuales con estacionalidad), usar la
  variante modificada de Mann-Kendall (paquete `pymannkendall`).
- Con pocos períodos (< 8), el p-valor es discreto y puede fallar en detectar
  tendencias reales (baja potencia).
