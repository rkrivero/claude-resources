# Módulo 6 — Regímenes espaciales y test de Chow (Anselin)

Modelo global = un coeficiente para toda la ciudad. GWR = variación suave y
continua. Regímenes = saltos DISCRETOS entre submercados (centro vs. periferia
informal). Validar antes de elegir GWR.

```python
import numpy as np
from spreg import OLS_Regimes, ML_Lag_Regimes

def regimenes(y, X, regimes, w=None, nombres_x=None):
    """
    regimes: vector categórico (p. ej. 'centro'/'intermedio'/'periferia') por observación.
    Lee el test de Chow: si es significativo, los coeficientes difieren entre regímenes.
    """
    if w is None:
        m = OLS_Regimes(y, X, regimes, name_x=nombres_x, regime_err_sep=False)
    else:
        w.transform = "r"
        m = ML_Lag_Regimes(y, X, regimes, w=w, name_x=nombres_x)
    print(m.summary)
    # Chow global
    chow_stat, chow_p = m.chow.joint
    print(f"Chow global: stat={chow_stat:.3f} p={chow_p:.4f} "
          f"{'→ dividir en regímenes' if chow_p < 0.05 else '→ no hay evidencia de regímenes'}")
    return m

def facetas_por_regimen(gdf, x, y, regimen_col):
    """Dispersión facetada: muestra el cambio de pendiente entre submercados."""
    import seaborn as sns
    sns.lmplot(data=gdf, x=x, y=y, col=regimen_col, height=3.2,
               scatter_kws=dict(s=8, alpha=0.5))
```

Render del mapa por regímenes: bloques de color por subregión con fronteras
estructurales gruesas (`linewidth>=1.5`, color oscuro), NO superficie suavizada.
Pasar al skill `composicion-lienzo-cartografico` para el acabado.

Regla de decisión: Chow significativo + fronteras conocidas ⇒ regímenes
(discontinuidad). Heterogeneidad suave sin fronteras claras ⇒ GWR/MGWR (Skill
`cartografia-modelos-inferencia`).
