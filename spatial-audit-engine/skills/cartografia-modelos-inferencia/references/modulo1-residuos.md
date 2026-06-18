# Módulo 1 — Auditoría de residuos y no-estacionariedad

```python
import numpy as np
import matplotlib.pyplot as plt
import mapclassify as mc
from esda.moran import Moran

def moran_residuos(model, w):
    """Autocorrelación remanente en los errores del modelo global."""
    e = np.asarray(model.u).flatten()      # residuos crudos e = y - ŷ
    w.transform = "r"
    mi = Moran(e, w, permutations=999)
    print(f"Moran de residuos: I={mi.I:.4f}  p={mi.p_sim:.4f}")
    if mi.p_sim < 0.05:
        print("→ El modelo global VIOLA independencia de errores. Justifica modelado local.")
    return mi

def leer_heterocedasticidad(model):
    """Breusch-Pagan / Koenker-Bassett / Jarque-Bera desde spreg (spat_diag/nonspat)."""
    for k in ("breusch_pagan", "koenker_bassett", "jarque_bera", "white"):
        v = getattr(model, k, None)
        if v is not None:
            print(f"{k}: {v}")

def mapa_residuos(gdf, model, ax=None):
    """Coropleta de residuos por desviación estándar, simétrica en 0."""
    gdf = gdf.copy()
    gdf["resid"] = np.asarray(model.u).flatten()
    # StdMean = cortes en múltiplos de sigma alrededor de la media (~0 en OLS)
    clf = mc.StdMean(gdf["resid"].values)
    ax = gdf.plot(column="resid", scheme="stdmean", cmap="RdBu_r",
                  legend=True, edgecolor="0.8", linewidth=0.2, ax=ax)
    ax.set_axis_off()
    ax.set_title("Diagnóstico de error: sobre/subestimación")
    return ax, clf
```

Cortes simétricos por desviación estándar (`StdMean`/`StdDeviation`) anclados en
0: rojo = subestimación (residuo positivo), azul = sobreestimación (negativo).
Aísla clusters de error que delatan variables geográficas omitidas.
