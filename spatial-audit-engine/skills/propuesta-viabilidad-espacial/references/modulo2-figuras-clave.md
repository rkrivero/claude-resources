# Módulo 2 — Las cuatro figuras de la propuesta

Solo la figura 1 (mapa dual de cobertura) es código nuevo; las demás reusan los
skills de análisis (ver punteros). Todas terminan con el acabado del skill
`composicion-lienzo-cartografico`.

## Figura 1 — Mapa dual de cobertura y densidad (NUEVO)

```python
import matplotlib.pyplot as plt
import contextily as cx
from scipy.stats import gaussian_kde
import numpy as np

def mapa_cobertura_dual(gdf_puntos):
    """Izquierda: puntos crudos (alpha=0.6). Derecha: superficie KDE."""
    g = gdf_puntos.to_crs(3857)   # web mercator para teselas
    fig, (axL, axR) = plt.subplots(1, 2, figsize=(12, 6))

    # --- izquierda: ploteo crudo ---
    g.plot(ax=axL, markersize=12, color="#1f4e79", alpha=0.6)
    cx.add_basemap(axL, source=cx.providers.CartoDB.PositronNoLabels, alpha=0.5)
    axL.set_title("Cobertura: 380 transacciones", fontsize=10); axL.set_axis_off()

    # --- derecha: densidad KDE ---
    xy = np.vstack([g.geometry.x, g.geometry.y])
    kde = gaussian_kde(xy)
    xmin, ymin, xmax, ymax = g.total_bounds
    xx, yy = np.mgrid[xmin:xmax:200j, ymin:ymax:200j]
    zz = kde(np.vstack([xx.ravel(), yy.ravel()])).reshape(xx.shape)
    axR.imshow(np.rot90(zz), extent=[xmin, xmax, ymin, ymax], cmap="viridis", alpha=0.7)
    g.plot(ax=axR, markersize=4, color="white", alpha=0.4)
    cx.add_basemap(axR, source=cx.providers.CartoDB.PositronNoLabels, alpha=0.4)
    axR.set_title("Densidad de la muestra (KDE)", fontsize=10); axR.set_axis_off()
    fig.tight_layout()
    return fig
```

Lee: si la KDE cubre el continuo urbano (zona central ↔ zonas periféricas),
hay variación espacial suficiente para calibrar un modelo local.

## Figura 2 — EDA de asimetría y submercados

Reusar `eda-esda-diagnostico/references/modulo2-eda-distribucion.md`:
`jointplot_densidad` (precio vs. determinante, con marginales) + `box_condicional`
o un `violinplot` por distrito. Objetivo: mostrar la cola larga a la derecha que
justifica log + análisis multi-escala.

## Figura 3 — Grafo de conectividad (define W)

Reusar `eda-esda-diagnostico/references/modulo3-pesos-espaciales.md`:
`construir_W` (KNN k=4 o 6 para puntos) + `auditar_islas` + `grafo_conectividad`,
sobre el mapa base mudo de `composicion-lienzo-cartografico/references/modulo4-contextily.md`.
Objetivo: demostrar que `W` está computada y sin islas artificiales por barreras.

## Figura 4 — Diagrama de dispersión de Moran

Reusar `eda-esda-diagnostico/references/modulo4-moran-global.md`:
`moran_global` + `diagrama_moran` (o `splot.esda.moran_scatterplot`). Objetivo:
pendiente positiva + p<0.05 ⇒ autocorrelación significativa ⇒ el OLS viola
independencia ⇒ el enfoque espacial es necesidad estadística.
