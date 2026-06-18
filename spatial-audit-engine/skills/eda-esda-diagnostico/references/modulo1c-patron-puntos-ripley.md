# Módulo 1c — Patrón de puntos multiescala: función K de Ripley / L de Besag

Ripley: el patrón cambia (agregación ↔ dispersión) según la escala. Estima el
radio óptimo de interacción ANTES de fijar el ancho de banda KDE o la banda de
distancia econométrica.

```python
import numpy as np
import matplotlib.pyplot as plt
from pointpats import PointPattern
from pointpats.distance_statistics import k_test, l_test

def ripley_radio_optimo(gdf_puntos, support=40, n_sim=99):
    """
    Corre L(d) (transformación estabilizada de K) vs. envolvente de CSR.
    Devuelve la distancia de máxima desviación positiva = radio de interacción.
    """
    coords = np.c_[gdf_puntos.geometry.x, gdf_puntos.geometry.y]
    lt = l_test(coords, support=support, permutations=n_sim, keep_simulations=True)

    d = lt.support
    obs = lt.statistic               # L(d) observada (centrada: L(d)-d)
    sims = lt.simulations            # (n_sim, support)
    hi = sims.max(axis=0)            # banda superior de CSR

    desviacion = obs - hi
    idx = int(np.argmax(desviacion))
    radio = d[idx]
    print(f"Radio óptimo de interacción (máx desviación sobre CSR): {radio:.1f} m")

    plt.plot(d, obs, color="crimson", label="L(d) observada")
    plt.fill_between(d, sims.min(axis=0), hi, color="0.8", label="envolvente CSR")
    plt.axvline(radio, ls="--", color="k", lw=0.7)
    plt.xlabel("distancia d (m)"); plt.ylabel("L(d)"); plt.legend()
    return radio
```

Uso del resultado:
- Ancho de banda fijo del KDE = `radio`.
- Banda de distancia econométrica (`libpysal.weights.DistanceBand`) con
  `threshold=radio`.

Si `obs` queda dentro de la envolvente en todo el rango ⇒ no se rechaza CSR (los
puntos son aleatorios) y un mapa de calor no aporta estructura real.
