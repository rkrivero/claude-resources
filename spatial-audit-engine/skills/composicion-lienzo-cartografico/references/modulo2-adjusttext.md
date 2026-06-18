# Módulo 2 — Control de colisiones de etiquetas (adjustText)

## Placement preferido por tipo de entidad (Brewer, Cap. 7)

### Puntos
Brewer define 6 posiciones preferidas en orden de prioridad (mayor → menor):
1. Upper-right (arriba-derecha) — default
2. Right (derecha)
3. Upper-left (arriba-izquierda)
4. Below-right (abajo-derecha)
5. Below-left (abajo-izquierda)
6. Left (izquierda)

Posición directamente arriba o abajo: evitar cuando hay puntos vecinos en esa
dirección (crea ambigüedad de anclaje). adjustText respeta estas prioridades
si se inicializa el texto en upper-right antes de la repulsión.

### Líneas
Etiqueta paralela a la dirección de la línea; no repetir hasta ≥50 unidades del mapa;
nunca cortar el trazo con la etiqueta. No usar adjustText para líneas — colocar
manualmente a lo largo de la geometría.

### Áreas
Combinar tracking + posición centrada. El texto debe caber DENTRO del polígono
si el área lo permite. Para áreas elongadas, curvar la etiqueta suavemente
siguiendo el eje mayor. Ver `modulo13-fuentes-tipografia.md` para tracking.

### Callouts (leader lines)
Usar SOLO en zonas saturadas. La línea líder debe ser la más corta posible
y anclar al punto más cercano del texto (no al centro del bbox).

```python
from adjustText import adjust_text
from shapely.geometry import Point

def etiquetar_puntos(ax, gdf, campo_texto, fontsize=7):
    """
    Etiquetas de puntos: inicio en upper-right (ha='left', va='bottom')
    antes de la repulsión. adjustText conserva el desplazamiento inicial
    como dirección preferida.
    """
    textos = []
    for _, row in gdf.iterrows():
        geom = row.geometry
        x, y = (geom.x, geom.y) if geom.geom_type == "Point" else (
            geom.centroid.x, geom.centroid.y)
        t = ax.text(x, y, str(row[campo_texto]),
                    fontsize=fontsize, ha="left", va="bottom")  # upper-right seed
        textos.append(t)
    adjust_text(
        textos, ax=ax,
        expand_text=(1.2, 1.4), expand_points=(1.1, 1.3),
        force_text=(0.4, 0.6), force_points=(0.3, 0.5),
        arrowprops=dict(arrowstyle="-", color="0.4", lw=0.35),
        only_move={"text": "xy"},
    )
    return textos

def etiquetar_poligonos(ax, gdf, campo_texto, fontsize=7):
    """
    Etiquetas de área: centroide, alineación central, tracking implícito.
    Usar modulo13 `etiqueta_area_tracking` para polígonos extensos.
    """
    textos = []
    for _, row in gdf.iterrows():
        c = row.geometry.centroid
        t = ax.text(c.x, c.y, str(row[campo_texto]),
                    fontsize=fontsize, ha="center", va="center",
                    clip_on=True)  # no salir del polígono si el bbox lo evita
        textos.append(t)
    adjust_text(
        textos, ax=ax,
        expand_text=(1.1, 1.2), expand_points=(1.0, 1.1),
        force_text=(0.2, 0.3), force_points=(0.1, 0.2),
        arrowprops=dict(arrowstyle="-", color="0.5", lw=0.25),
        only_move={"text": "xy"},
    )
    return textos

def etiquetar_sin_colision(ax, gdf, campo_texto, fontsize=7):
    """Selección automática de función según tipo de geometría."""
    tipo = gdf.geometry.geom_type.iloc[0]
    if tipo in ("Point", "MultiPoint"):
        return etiquetar_puntos(ax, gdf, campo_texto, fontsize)
    else:
        return etiquetar_poligonos(ax, gdf, campo_texto, fontsize)
```

Regla: en zonas saturadas (centro consolidado), desplazar el texto fuera del
punto crítico y trazar una línea de llamada de 0.2–0.5 pt que preserva el
anclaje espacial. Fuerza de atracción a la coordenada real, repulsión a textos
vecinos y bordes. Combinar con `modulo13-fuentes-tipografia.md` para aplicar
las convenciones sans-serif/serif según tipo de entidad.
