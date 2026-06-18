# Módulo 3 — Contraste frente-fondo (halos / path effects)

```python
import matplotlib.patheffects as pe

def halo(color_texto="black"):
    """Halo perimetral inverso al tono del texto. 1.5–2.5 pt. Sin fondo opaco."""
    color_halo = "white" if color_texto in ("black", "0.0", "#000000") else "0.15"
    return [pe.withStroke(linewidth=2.0, foreground=color_halo)]

def texto_con_halo(ax, x, y, s, color="black", fontsize=8):
    return ax.text(x, y, s, color=color, fontsize=fontsize,
                   ha="center", va="center",
                   path_effects=halo(color))
```

Regla: PROHIBIR fondos cuadrados opacos detrás del texto (ocultan el mapa). Usar
halo de 1.5–2.5 pt con color inverso (halo blanco para texto oscuro; halo oscuro
para texto claro). Aísla el texto del ruido del mapa base sin romper la
continuidad de la capa temática.
