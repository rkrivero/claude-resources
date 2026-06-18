# Módulo 4 — Codificación cromática divergente anclada en cero

```python
import numpy as np
from matplotlib.colors import TwoSlopeNorm

def norm_divergente_en_cero(valores_significativos):
    """Ancla rígidamente el color de transición (blanco) en 0.0."""
    v = np.asarray(valores_significativos)
    v = v[~np.isnan(v)]
    vmin, vmax = v.min(), v.max()
    if vmin < 0 < vmax:
        # rango asimétrico: TwoSlopeNorm mantiene el 0 en el centro cromático
        norm = TwoSlopeNorm(vmin=vmin, vcenter=0.0, vmax=vmax)
        print(f"Rango asimétrico [{vmin:.3f}, {vmax:.3f}] — cero anclado en el blanco.")
    else:
        # no cambia de signo: una rampa secuencial sería más honesta
        norm = TwoSlopeNorm(vmin=min(vmin, -1e-9), vcenter=0.0, vmax=max(vmax, 1e-9))
        print("AVISO: la variable no cambia de signo; considerar rampa secuencial.")
    return norm

# Paletas divergentes recomendadas: 'RdBu_r', 'coolwarm', 'BrBG' (ColorBrewer).
# Positivo saturado = rojo; negativo = azul; transición = blanco == 0.
# VETAR rojo-verde (RdYlGn/Spectral): ~8% de daltonismo. Auditar con el módulo
# de color accesible del Skill 4 (composicion-lienzo-cartografico/modulo6).
```

Regla: toda variable con coeficiente de signo cambiante usa paleta divergente
simétrica con `TwoSlopeNorm(vcenter=0)`. El blanco coincide MATEMÁTICAMENTE con
el cero, no con la media muestral.
