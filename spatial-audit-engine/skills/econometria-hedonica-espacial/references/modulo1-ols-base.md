# Módulo 1 — Línea base OLS y diagnóstico (spreg)

```python
import numpy as np
from spreg import OLS

def ols_base(y, X, w, nombres_x):
    """y:(n,1), X:(n,k). spat_diag+moran activan los tests para el árbol LM."""
    model = OLS(y, X, w=w, spat_diag=True, moran=True,
                name_y="ln_precio", name_x=nombres_x)
    print(model.summary)
    return model

def leer_diagnosticos(model):
    out = {
        "r2": model.r2,
        "condition_number": getattr(model, "condition_number", None),  # multicolinealidad
        "jarque_bera_p": model.jarque_bera["pvalue"],                   # normalidad
        "breusch_pagan_p": model.breusch_pagan["pvalue"],               # heterocedasticidad
        "koenker_bassett_p": model.koenker_bassett["pvalue"],
        "moran_resid": model.moran_res,   # (I, z, p) de residuos
    }
    if out["moran_resid"][2] < 0.05:
        print("Moran de residuos significativo → hay estructura espacial. Ir al árbol LM.")
    return out
```

Si Moran de residuos no es significativo y no hay LM significativo, el OLS basta.
Si hay estructura espacial, pasar al Módulo 2.
