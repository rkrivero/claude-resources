# Módulo 1 — Jerarquía visual y balance del espacio vacío

```python
import matplotlib.pyplot as plt

def lienzo_base(figsize=(8, 10)):
    """Centro visual ~5% por encima del centro geométrico."""
    fig = plt.figure(figsize=figsize)
    # eje principal del mapa, ligeramente elevado para subir el centro óptico
    ax_map = fig.add_axes([0.06, 0.18, 0.88, 0.76])   # [left, bottom, w, h]
    ax_map.set_axis_off()
    return fig, ax_map

def jerarquia_tres_niveles(fig, ax_map):
    """
    1. Primer plano: capa econométrica significativa (sobre ax_map).
    2. Segundo plano: referencia territorial + escala.
    3. Soporte: título, leyenda, fuentes — alineados al marco invisible.
    PROHIBIDO: recuadros negros (bounding boxes) alrededor de elementos.
    """
    # título alineado a la izquierda del marco (sin caja)
    fig.text(0.06, 0.95, "Título del mapa", ha="left", va="top",
             fontsize=11, fontweight="medium")
    # fuente/metadatos abajo, peso de soporte, sin recuadro
    fig.text(0.06, 0.045, "Fuente: ... · Elaboración propia · CRS EPSG:[código]",
             ha="left", va="bottom", fontsize=7, color="0.35")
    return fig
```

Regla Brewer: ordenar con espacio vacío y alineación a los ejes invisibles del
marco, nunca con cajas que añaden peso muerto. Escala y norte van en segundo
plano (ver módulos siguientes para su estilo).

## Alineación intencional vs. accidental (Brewer)

Elementos CASI alineados (diferencia de 1–4 pt) parecen errores de descuido.
Regla: o alinear PERFECTAMENTE o separar con una diferencia **≥ 8 pt** que
el ojo lea como separación deliberada. No existe el "más o menos alineado".

```python
def verificar_alineacion(posiciones_y, tolerancia_pt=2, separacion_minima_pt=8):
    """
    Detecta alineamientos ambiguos en una lista de coordenadas Y (en puntos).
    Devuelve pares que están demasiado cerca para ser casuales pero demasiado
    lejos para alinearse con precisión.
    """
    alertas = []
    for i, a in enumerate(posiciones_y):
        for j, b in enumerate(posiciones_y):
            if i >= j:
                continue
            delta = abs(a - b)
            if tolerancia_pt < delta < separacion_minima_pt:
                alertas.append((i, j, delta))
    return alertas
```

## Proximidad escala-mapa (Brewer)

La barra de escala debe estar **más cerca del mapa al que pertenece**
que de cualquier otro elemento. En layouts con mapa principal + inset localizador,
cada mapa necesita su propia escala, o la escala va sin ambigüedad junto al
principal. Una escala huérfana en la esquina inferior sin relación de proximidad
clara es un error de comunicación.

```python
def agregar_barra_escala(ax, longitud_km, x=0.08, y=0.04,
                         linewidth=1.5, color="0.2"):
    """
    Barra de escala gráfica (resiste zoom/cambio de tamaño de figura).
    Preferir SIEMPRE sobre RF (1:X) que se rompe al cambiar tamaño de export.
    x, y en coordenadas de figura (0-1).
    """
    from matplotlib.lines import Line2D
    from matplotlib.patches import FancyArrowPatch
    fig = ax.get_figure()
    # convertir longitud_km a unidades del mapa (asume CRS métrico)
    xmin, xmax = ax.get_xlim()
    ancho_datos = xmax - xmin
    ancho_fig_inch = fig.get_figwidth() * (ax.get_position().width)
    px_por_unidad = ancho_fig_inch * fig.dpi / ancho_datos
    lon_unidades = longitud_km * 1000   # metros si CRS es UTM
    lon_fig = lon_unidades / ancho_datos * ax.get_position().width
    # trazar la barra
    barra = plt.Line2D([x, x + lon_fig], [y, y],
                       transform=fig.transFigure,
                       color=color, linewidth=linewidth)
    fig.add_artist(barra)
    fig.text(x + lon_fig / 2, y - 0.015, f"{longitud_km} km",
             ha="center", va="top", fontsize=6.5, color=color,
             transform=fig.transFigure)
    return barra
```

**Anti-patrón**: norte arrow elaborado / rosa de los vientos en mapa con cuadrícula UTM.
Un símbolo simple (línea vertical + "N") es suficiente. Si el mapa está orientado
al norte convencional y tiene ticks de latitud/longitud, el norte arrow es redundante.
