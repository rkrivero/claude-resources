# Compuerta 2 — Validación cruzada espacial (Spatial Block CV)

El train/test aleatorio filtra información por vecindad (Tobler) → métricas
infladas. Bloquear geográficamente para medir poder predictivo real.

```python
import numpy as np
from sklearn.cluster import KMeans
from sklearn.model_selection import GroupKFold
from sklearn.metrics import mean_absolute_error, r2_score

def bloques_espaciales(coords, n_bloques=10, random_state=0):
    """Agrupa observaciones en bloques contiguos por sus coordenadas."""
    return KMeans(n_clusters=n_bloques, random_state=random_state, n_init=10).fit_predict(coords)

def spatial_block_cv(modelo, X, y, coords, n_bloques=10):
    """
    Entrena en unos bloques y prueba en uno alejado. Compara con CV aleatorio
    para detectar leakage (la brecha = optimismo del CV aleatorio).
    """
    grupos = bloques_espaciales(coords, n_bloques)
    gkf = GroupKFold(n_splits=n_bloques)
    maes, r2s = [], []
    for tr, te in gkf.split(X, y, groups=grupos):
        modelo.fit(X[tr], y[tr])
        pred = modelo.predict(X[te])
        maes.append(mean_absolute_error(y[te], pred))
        r2s.append(r2_score(y[te], pred))
    print(f"Spatial Block CV → MAE={np.mean(maes):.3f}  R²={np.mean(r2s):.3f}")
    return {"mae": np.mean(maes), "r2": np.mean(r2s)}
```

Reportar SIEMPRE el R²/MAE del block CV (no del aleatorio). Si el desempeño cae
mucho frente al CV aleatorio, el modelo estaba copiando vecinos, no aprendiendo
determinantes estructurales. Paquete alternativo dedicado: `spacv` o
`verstack`/`mlxtend` no requeridos — `scikit-learn` basta con bloques KMeans.
