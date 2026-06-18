# Módulo 1b/7 — Modelo SLX (Spatial Lag of X)

Punto de partida recomendado: spillovers LOCALES puros, estimación OLS directa,
sin la endogeneidad ni los spillovers globales de SAR.

```python
import numpy as np
from libpysal.weights import lag_spatial
from spreg import OLS

def slx(y, X, w, nombres_x):
    """Añade WX (rezago de las independientes) al OLS tradicional."""
    w.transform = "r"
    WX = np.column_stack([lag_spatial(w, X[:, j]) for j in range(X.shape[1])])
    Xslx = np.hstack([X, WX])
    nombres = list(nombres_x) + [f"W_{n}" for n in nombres_x]
    m = OLS(y, Xslx, w=w, spat_diag=True, moran=True, name_x=nombres, name_y="ln_precio")
    print(m.summary)
    return m
```

Interpretación:
- Coeficiente de `X_j` = efecto directo del atributo propio.
- Coeficiente de `W_X_j` = efecto del MISMO atributo en las vecinas (spillover
  local). Significativo ⇒ el entorno inmediato importa.

Reglas:
- Si los `W_X` son significativos y el Moran de residuos del SLX ya no lo es, SLX
  basta: no escalar a SAR/SEM.
- Si el Moran de residuos persiste, recién ahí pasar al árbol LM (Módulo 2).
- En SLX los efectos directo/indirecto se leen directo de los coeficientes (no
  requieren la corrección `(I-ρW)^-1` de los modelos con `Wy`).
