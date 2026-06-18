# Módulo 5 — Validación y selección final

```python
import numpy as np
from esda.moran import Moran

def moran_modelo(m, w):
    """Idealmente NO significativo: el modelo absorbió la autocorrelación."""
    e = np.asarray(m.u).flatten()
    mi = Moran(e, w, permutations=999)
    ok = mi.p_sim >= 0.05
    print(f"Moran de residuos del modelo: I={mi.I:.4f} p={mi.p_sim:.4f} "
          f"{'OK (sin autocorrelación)' if ok else 'PERSISTE estructura espacial'}")
    return mi, ok

def comparar_modelos(metricas_por_modelo):
    """Menor AIC + mayor logL + Moran no significativo."""
    for nombre, met in metricas_por_modelo.items():
        print(f"{nombre}: AIC={met.get('aic')}  logL={met.get('logll')}")
    return min(metricas_por_modelo, key=lambda k: metricas_por_modelo[k].get("aic", 1e18))
```

Reglas de cierre:
- Coeficientes con signos coherentes con la teoría hedónica (distancia al centro
  negativa sobre el precio, etc.).
- Si la heterogeneidad espacial PERSISTE (residuos aún agrupados o ajuste variable
  por zona), NO forzar un modelo global → derivar al skill
  `cartografia-modelos-inferencia` para GWR/MGWR local.
- Exportar: modelo elegido + justificación del árbol LM, residuos y tabla de
  efectos para alimentar la cartografía de inferencia.
```
