# Módulo 12 — Leyenda con mini-histograma (desarmar el salto artificial)

Las clases de Fisher-Jenks crean fronteras que el ojo lee como saltos
cualitativos. El mini-histograma muestra cuántos datos reales caen en cada corte.

```python
import numpy as np
import matplotlib.pyplot as plt
from matplotlib import cm
import mapclassify as mc

def coropleta_con_histograma(gdf, col, k=6, cmap="viridis"):
    clf = mc.FisherJenks(gdf[col].values, k=k)
    cmap_o = cm.get_cmap(cmap, k)

    fig = plt.figure(figsize=(8, 9))
    ax_map = fig.add_axes([0.05, 0.28, 0.90, 0.68]); ax_map.set_axis_off()
    gdf.assign(_c=clf.yb).plot(column="_c", categorical=True, cmap=cmap_o,
                               ax=ax_map, edgecolor="white", linewidth=0.2, legend=False)

    # mini-histograma con barras coloreadas por clase
    ax_h = fig.add_axes([0.12, 0.07, 0.76, 0.15])
    vals = gdf[col].values
    n, bins, patches = ax_h.hist(vals, bins=40, color="0.7")
    cortes = list(clf.bins)
    for p, b in zip(patches, bins[:-1]):
        clase = np.searchsorted(cortes, b, side="right")
        clase = min(clase, k - 1)
        p.set_facecolor(cmap_o(clase))
    for c in cortes[:-1]:
        ax_h.axvline(c, color="0.2", lw=0.6, ls="--")   # fronteras de clase
    ax_h.set_yticks([]); ax_h.set_xlabel(col, fontsize=8)
    for s in ("top", "right", "left"): ax_h.spines[s].set_visible(False)
    return fig
```

Regla: toda coropleta clasificada lleva su mini-histograma con barras del color de
su clase y las fronteras marcadas. Así el revisor ve a la vez la geografía y la
densidad real de datos por corte, desmontando la ilusión del salto mágico.
