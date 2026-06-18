# Compuerta 5 — Detrending (correlación espuria por topografía)

Quita la tendencia geográfica de fondo antes de atribuir un efecto a una variable.

```python
import numpy as np
from spreg import OLS

def detrend_por_coordenadas(y, coords, grado=1, elevacion=None):
    """
    Regresa y contra (X, Y) [y opcional elevación/MDE] y devuelve residuos limpios.
    grado=2 añade términos cuadráticos para una superficie de tendencia más flexible.
    """
    x, yy = coords[:, 0], coords[:, 1]
    cols = [x, yy]
    if grado >= 2:
        cols += [x**2, yy**2, x*yy]
    if elevacion is not None:
        cols.append(np.asarray(elevacion))
    Z = np.column_stack(cols)
    m = OLS(y.reshape(-1, 1), Z)
    resid = np.asarray(m.u).flatten()      # valor del suelo "limpio" del relieve
    print(f"Tendencia geográfica R²={m.r2:.3f} (cuánto del precio explica solo la geografía)")
    return resid

def relacion_persiste(resid, X_morfo, w):
    """Corre el modelo sobre los residuos detrendizados; si la relación persiste, es real."""
    m = OLS(resid.reshape(-1, 1), X_morfo, w=w, spat_diag=True, moran=True)
    print(m.summary)
    return m
```

Regla: si el coeficiente de una variable morfológica sigue significativo y con el
mismo signo sobre los residuos detrendizados, el determinante es real. Si se
desvanece, solo seguía el contorno de la pendiente/accesibilidad. Documentar el R²
de la superficie de tendencia (cuánto explicaba la pura geografía).
