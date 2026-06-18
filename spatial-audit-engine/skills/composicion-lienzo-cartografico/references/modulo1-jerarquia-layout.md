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
    fig.text(0.06, 0.045, "Fuente: ... · Elaboración propia · CRS EPSG:32719",
             ha="left", va="bottom", fontsize=7, color="0.35")
    return fig
```

Regla Brewer: ordenar con espacio vacío y alineación a los ejes invisibles del
marco, nunca con cajas que añaden peso muerto. Escala y norte van en segundo
plano (ver módulos siguientes para su estilo).
