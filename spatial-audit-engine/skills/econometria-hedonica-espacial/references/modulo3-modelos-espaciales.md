# Módulo 3 — Estimación de modelos espaciales (spreg)

```python
from spreg import ML_Lag, ML_Error, GM_Combo_Het, GM_Error_Het
import numpy as np

def estimar(decision, y, X, w, nombres_x):
    w.transform = "r"
    if decision == "SLM":
        m = ML_Lag(y, X, w=w, name_x=nombres_x, name_y="ln_precio")
    elif decision == "SEM":
        m = ML_Error(y, X, w=w, name_x=nombres_x, name_y="ln_precio")
    elif decision.startswith("SAC"):
        m = GM_Combo_Het(y, X, w=w, name_x=nombres_x, name_y="ln_precio")
    elif decision == "SDM":
        # Durbin: añadir rezagos espaciales de las X (WX)
        from libpysal.weights import lag_spatial
        WX = np.hstack([lag_spatial(w, X[:, i]).reshape(-1, 1) for i in range(X.shape[1])])
        Xd = np.hstack([X, WX])
        nombres = nombres_x + [f"W_{n}" for n in nombres_x]
        m = ML_Lag(y, Xd, w=w, name_x=nombres, name_y="ln_precio")
    else:
        raise ValueError(decision)
    print(m.summary)
    return m

def metricas(m):
    return {"pr2": getattr(m, "pr2", None),
            "logll": getattr(m, "logll", None),
            "aic": getattr(m, "aic", None)}
```

SDM se reduce a SEM si el test de Common Factor no se rechaza (los `W_X` no
aportan). Para heterocedasticidad fuerte preferir las variantes `GM_*_Het`.
