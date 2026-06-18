# Compuerta 1 — VIF local en GWR/MGWR

VIF local por punto de calibración. Umbral estricto 5 (laxo 10). Donde se supera,
el coeficiente local es artefacto del colapso matemático, no efecto real.

```python
import numpy as np
from mgwr.sel_bw import Sel_BW
from mgwr.gwr import GWR

def vif_local(coords, X, bw):
    """
    VIF local: para cada punto i, ajusta una regresión local ponderada de cada
    variable contra las demás y calcula 1/(1-R²_local).
    X: (n,k) SIN intercepto. Devuelve (n,k) de VIF.
    """
    n, k = X.shape
    # pesos gaussianos por distancia (bisquare/gaussiano según el modelo)
    from scipy.spatial.distance import cdist
    D = cdist(coords, coords)
    Wt = np.exp(-0.5 * (D / bw) ** 2)          # kernel gaussiano
    vif = np.full((n, k), np.nan)
    for i in range(n):
        wi = Wt[i]
        for j in range(k):
            otras = np.delete(np.arange(k), j)
            Xo = np.c_[np.ones(n), X[:, otras]]
            yj = X[:, j]
            sw = np.sqrt(wi)
            beta, *_ = np.linalg.lstsq(Xo * sw[:, None], yj * sw, rcond=None)
            pred = Xo @ beta
            ss_res = np.sum(wi * (yj - pred) ** 2)
            ss_tot = np.sum(wi * (yj - np.average(yj, weights=wi)) ** 2)
            r2 = 1 - ss_res / ss_tot if ss_tot > 0 else 0
            vif[i, j] = 1.0 / (1.0 - r2) if r2 < 1 else np.inf
    return vif

def alerta_vif(gdf, vif, idx, nombre, umbral=5.0):
    """Marca los sectores donde el VIF local de la variable supera el umbral."""
    gdf = gdf.copy()
    gdf[f"vif_{nombre}"] = vif[:, idx]
    gdf[f"vif_alerta_{nombre}"] = gdf[f"vif_{nombre}"] > umbral
    n_alerta = int(gdf[f"vif_alerta_{nombre}"].sum())
    print(f"{nombre}: {n_alerta}/{len(gdf)} sectores con VIF_local>{umbral} (excluir de la discusión)")
    return gdf
```

Render: superponer hatch/transparencia sobre los sectores en alerta del mapa de
coeficientes (reutilizar la máscara del Skill 2). Documentar el umbral usado.
