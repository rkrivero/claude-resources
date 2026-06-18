# Módulo 10 — Data-ink ratio y anti-chartjunk (Tufte)

Maximizar la proporción de tinta que comunica datos; eliminar el resto.

```python
import matplotlib.pyplot as plt
import numpy as np

def coropleta_limpia(gdf, col, cmap="RdBu_r", ax=None):
    """Sin marco, sin cuadrícula, bordes casi invisibles."""
    ax = gdf.plot(column=col, cmap=cmap, legend=True,
                  edgecolor="white", linewidth=0.2, ax=ax) if ax is None else \
         gdf.plot(column=col, cmap=cmap, legend=True,
                  edgecolor="white", linewidth=0.2, ax=ax)
    ax.set_axis_off()                 # fuera ejes, marcos y ticks
    for s in ax.spines.values():
        s.set_visible(False)
    return ax

def norte_mudo(ax, x=0.95, y=0.95, largo=0.06):
    """Flecha de norte minimalista: línea vertical + 'N'. NO brújula decorativa."""
    ax.annotate("", xy=(x, y), xytext=(x, y - largo), xycoords="axes fraction",
                arrowprops=dict(arrowstyle="-|>", color="0.3", lw=1.0))
    ax.text(x, y + 0.01, "N", transform=ax.transAxes, ha="center",
            va="bottom", fontsize=8, color="0.3")

def escala_muda(ax, gdf, longitud_m=1000, x=0.05, y=0.05):
    """Barra de escala = línea delgada con pocas marcas, sin bloques alternados."""
    xmin, ymin, xmax, ymax = gdf.total_bounds
    ancho_ejes = longitud_m / (xmax - xmin)
    x1 = x + ancho_ejes
    ax.plot([x, x1], [y, y], transform=ax.transAxes, color="0.3", lw=1.0)
    for xx, lab in [(x, "0"), (x1, f"{longitud_m/1000:g} km")]:
        ax.plot([xx, xx], [y, y + 0.008], transform=ax.transAxes, color="0.3", lw=1.0)
        ax.text(xx, y + 0.012, lab, transform=ax.transAxes, ha="center",
                va="bottom", fontsize=7, color="0.3")
```

Reglas anti-chartjunk:
- Sin marcos dobles ni cuadrícula gruesa; ejes ocultos.
- Bordes de polígono blancos/gris `alpha≈0.3`, `linewidth≈0.2` (en centro denso el
  negro tapa el color del dato).
- Norte y escala mudos y pequeños: son referencia, no protagonistas.
- Si un elemento no comunica un dato, eliminarlo.
