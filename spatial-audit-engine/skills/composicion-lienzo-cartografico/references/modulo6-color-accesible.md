# Módulo 6 — Color accesible y deficiencia cromática (Healy, Wilke)

~8% de hombres tienen deuteranopía/protanopía. La paleta semáforo rojo-verde es
un error grave para residuos o cambios de valor.

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import cm

DIVERGENTES_OK = ["RdBu_r", "PuOr", "BrBG", "coolwarm"]   # seguros, NO rojo-verde
SECUENCIALES_OK = ["YlGnBu", "viridis", "Purples", "YlGn"]
VETADAS = ["RdYlGn", "Spectral", "jet", "hsv"]            # rojo-verde / no perceptuales

def auditar_paleta(nombre):
    if nombre in VETADAS:
        raise ValueError(f"Paleta '{nombre}' vetada (rojo-verde / no perceptual). "
                         f"Usar una de {DIVERGENTES_OK} o {SECUENCIALES_OK}.")
    return nombre

def simular_daltonismo(cmap_name, tipo="deuteranopia", n=256):
    """Simula cómo se ve la rampa con deficiencia cromática (requiere colorspacious)."""
    from colorspacious import cspace_convert
    rgb = cm.get_cmap(cmap_name)(np.linspace(0, 1, n))[:, :3]
    cvd = {"name": "sRGB1+CVD", "cvd_type": tipo, "severity": 100}
    sim = cspace_convert(rgb, cvd, "sRGB1").clip(0, 1)
    fig, ax = plt.subplots(2, 1, figsize=(6, 1.4))
    ax[0].imshow([rgb]); ax[0].set_title(f"{cmap_name} (normal)", fontsize=8); ax[0].axis("off")
    ax[1].imshow([sim]); ax[1].set_title(tipo, fontsize=8); ax[1].axis("off")
    return sim

def chequeo_grises(cmap_name, n=256):
    """Luminosidad monótona => sobrevive impresión en escala de grises."""
    from colorspacious import cspace_convert
    rgb = cm.get_cmap(cmap_name)(np.linspace(0, 1, n))[:, :3]
    J = cspace_convert(rgb, "sRGB1", "CAM02-UCS")[:, 0]   # lightness
    # divergente: monótona hacia el centro neutro (sube y baja); secuencial: monótona total
    return J
```

Reglas: divergentes solo `RdBu`/`PuOr`/`BrBG` con luminosidad que crece hacia los
extremos y se neutraliza en 0; secuenciales perceptualmente uniformes (`viridis`,
`magma`, `plasma`, `inferno`, `YlGnBu`). Auditar siempre con `simular_daltonismo`
y `chequeo_grises` antes de exportar la figura final de la tesis.

Trampa del arcoíris (Brewer): `jet`/`rainbow` tienen luminancia NO monótona —
el amarillo brillante salta y el azul oscuro se esconde, creando fronteras falsas
("muros económicos") donde el dato cambia suave y ocultando variación en los
verdes. `chequeo_grises` lo delata: una rampa uniforme da luminosidad monótona;
`jet` da una curva en zigzag. Vetar toda paleta sin progresión de luminancia
monótona.
