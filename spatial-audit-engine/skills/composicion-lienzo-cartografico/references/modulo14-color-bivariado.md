# Módulo 14 — Esquemas de color bivariados (Brewer, Cap. 9)

Un mapa bivariado codifica **dos variables simultáneas** en la misma geometría.
Úsalo cuando quieras mostrar coeficiente GWR + error estándar, o densidad + vacancia,
en un único mapa sin perder la dimensión de ninguna variable.

## Tipos de combinación (Brewer)

| Tipo | Variable A | Variable B | Patrón luminosidad |
|---|---|---|---|
| Sequential-Sequential | continua ordenada | continua ordenada | diagonal claro→oscuro |
| Diverging-Diverging | cambia de signo | cambia de signo | dos planos que se cruzan |
| Qualitative-Sequential | categórica nominal | continua ordenada | tono × luminosidad |

## Sequential-Sequential — implementación

La lógica: paleta 3×3 donde cada celda es el color resultante de mezclar
el nivel de A (columnas) con el nivel de B (filas). Los extremos diagonales
son los saturados; las celdas off-diagonal revelan la tensión entre variables.

```python
import numpy as np
import matplotlib.pyplot as plt
import matplotlib.colors as mcolors
import geopandas as gpd

# Colores de la grilla 3×3 para un bivariado sequential-sequential.
# Filas: variable B (baja→alta); Columnas: variable A (baja→alta).
# Esquema inspirado en ColorBrewer: A=azul, B=rojo; ambos claros cuando bajo.
BIVAR_3X3 = np.array([
    ["#e8e8e8", "#ace4e4", "#5ac8c8"],   # B baja: blanco → azul
    ["#dfb0d6", "#a5add3", "#5698b9"],   # B media: rosado → violáceo
    ["#be64ac", "#8c62aa", "#3b4994"],   # B alta: fucsia → índigo
])
# Índice: [fila_B, col_A] → color hex

def clasificar_bivariado(serie, n_clases=3):
    """Devuelve etiqueta de clase 0,1,2 para n_clases cuantiles."""
    cortes = np.percentile(serie.dropna(), np.linspace(0, 100, n_clases + 1))
    return np.digitize(serie, cortes[1:-1])  # 0-indexed

def color_bivariado(gdf, var_a, var_b, paleta=BIVAR_3X3):
    """
    Asigna un color a cada fila del GeoDataFrame según la
    combinación de clases de var_a (columna) y var_b (fila).
    """
    clase_a = clasificar_bivariado(gdf[var_a])
    clase_b = clasificar_bivariado(gdf[var_b])
    gdf = gdf.copy()
    gdf["_color_biv"] = [paleta[b, a] for a, b in zip(clase_a, clase_b)]
    return gdf

def mapa_bivariado(gdf, var_a, var_b, label_a="Variable A", label_b="Variable B",
                   figsize=(9, 9)):
    gdf = color_bivariado(gdf, var_a, var_b)
    fig, ax = plt.subplots(figsize=figsize)
    gdf.plot(ax=ax, color=gdf["_color_biv"], linewidth=0.25, edgecolor="0.6")
    ax.set_axis_off()
    _leyenda_bivariada(fig, label_a, label_b)
    return fig, ax

def _leyenda_bivariada(fig, label_a, label_b, paleta=BIVAR_3X3,
                       pos=(0.06, 0.06), tamano=0.12):
    """Dibuja la grilla de colores 3×3 como leyenda en esquina inferior izquierda."""
    n = paleta.shape[0]
    cel = tamano / n
    ax_ley = fig.add_axes([pos[0], pos[1], tamano, tamano])
    for i in range(n):
        for j in range(n):
            ax_ley.add_patch(plt.Rectangle((j, i), 1, 1,
                                           color=paleta[i, j], linewidth=0))
    ax_ley.set_xlim(0, n); ax_ley.set_ylim(0, n)
    ax_ley.set_xticks([]); ax_ley.set_yticks([])
    ax_ley.set_xlabel(label_a, fontsize=6, labelpad=2)
    ax_ley.set_ylabel(label_b, fontsize=6, labelpad=2)
    # flechas de dirección
    ax_ley.annotate("", xy=(n, -0.6), xytext=(0, -0.6),
                    xycoords="data", textcoords="data",
                    arrowprops=dict(arrowstyle="->", lw=0.6, color="0.3"),
                    annotation_clip=False)
    ax_ley.annotate("", xy=(-0.6, n), xytext=(-0.6, 0),
                    xycoords="data", textcoords="data",
                    arrowprops=dict(arrowstyle="->", lw=0.6, color="0.3"),
                    annotation_clip=False)
```

## Caso de uso: coeficiente GWR + error estándar

```python
# Después de estimar MGWR:
# gdf["beta_dist"] = resultados.params[:, idx]
# gdf["se_dist"]   = resultados.bse[:, idx]

fig, ax = mapa_bivariado(
    gdf,
    var_a="beta_dist",
    var_b="se_dist",
    label_a="Coef. distancia",
    label_b="Error estándar",
)
# Interpretar: esquina superior-derecha (índigo oscuro) = coeficiente alto + error alto
#              esquina inferior-derecha (azul) = coeficiente alto + error bajo (ideal)
```

## Diverging-Diverging

Para dos variables que cambian de signo, reparametrizar las clases alrededor del 0:

```python
def clasificar_divergente(serie, n=3):
    """n=3: negativo/neutro/positivo según percentiles 33/67."""
    p = serie.dropna()
    cortes = [p.quantile(0.33), p.quantile(0.67)]
    return np.digitize(serie, cortes)

# Paleta diverging-diverging: rojo (neg) × azul (neg) en esquina inferior-izq,
# ambos positivos en esquina superior-der (verde saturado).
BIVAR_DIV = np.array([
    ["#ca0020", "#f4a582", "#f7f7f7"],
    ["#92c5de", "#d4d4d4", "#f4a582"],
    ["#0571b0", "#92c5de", "#ca0020"],
])
```

## Reglas de uso (Brewer)

- Máximo 3×3 clases (9 categorías). 4×4 supera la capacidad discriminativa del ojo.
- La leyenda DEBE ser la grilla de colores, no una lista. El lector necesita ver el espacio bidimensional.
- Acompañar con dos mapas univariados pequeños (small multiples) para el lector no entrenado.
- Auditar con `simular_daltonismo` (modulo6): verificar que las esquinas diagonales sean distinguibles en deuteranopía.
