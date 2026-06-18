# Módulo 8 — Small multiples (Tufte)

Comparar varios modelos sin saturar la memoria del lector: mismos encuadre,
cortes de clase y paleta; varía solo el parámetro estimado.

```python
import matplotlib.pyplot as plt
import mapclassify as mc

def small_multiples(gdf, columnas, titulos, cmap="RdBu_r", k=6, bounds=None):
    """
    columnas: lista de campos (ej. ['beta_ols','beta_gwr','beta_mgwr'])
    Cortes de clase COMPARTIDOS para que las leyendas sean idénticas.
    """
    # cortes comunes a partir del pool de todos los valores
    pool = gdf[columnas].values.flatten()
    pool = pool[~mc.classifiers._np.isnan(pool)] if hasattr(mc, "classifiers") else pool
    clf = mc.FisherJenks(pool, k=k)
    cortes = clf.bins

    n = len(columnas)
    fig, axes = plt.subplots(1, n, figsize=(4*n, 5), sharex=True, sharey=True)
    if n == 1:
        axes = [axes]
    for ax, col, tit in zip(axes, columnas, titulos):
        gdf.plot(column=col, ax=ax, cmap=cmap,
                 scheme="user_defined", classification_kwds={"bins": list(cortes)},
                 edgecolor="0.8", linewidth=0.2, legend=(ax is axes[-1]))
        ax.set_title(tit, fontsize=9); ax.set_axis_off()
        if bounds is not None:
            ax.set_xlim(bounds[0], bounds[2]); ax.set_ylim(bounds[1], bounds[3])
    fig.tight_layout()
    return fig
```

Regla: una sola leyenda compartida (o idéntica) al final del panel. Nunca cambiar
escala ni paleta entre paneles: eso obliga al lector a recalibrar y anula la
comparación.

## Coeficientes estandarizados para unificar unidades (Wilke)

Si los coeficientes vienen en unidades distintas (precio/grado de pendiente vs.
precio/metro), no son comparables entre paneles. Solución: estandarizar las
variables independientes a puntuaciones Z ANTES de estimar el MGWR (ver Skill
`cartografia-modelos-inferencia`, modulo2: `estandarizar_X`). Así todos los `β_i`
quedan en "impacto por desviación estándar" y el panel comparte una sola leyenda
divergente anclada en 0 (p. ej. −3 a +3):

```python
from matplotlib.colors import TwoSlopeNorm
norm = TwoSlopeNorm(vmin=-3, vcenter=0, vmax=3)   # misma para todos los paneles
# pasar norm a cada gdf.plot(..., norm=norm) en lugar de cortes por clase
```
