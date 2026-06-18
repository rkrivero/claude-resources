# Módulo 2 — Control de colisiones de etiquetas (adjustText)

```python
from adjustText import adjust_text

def etiquetar_sin_colision(ax, gdf, campo_texto, max_n=40):
    """Repulsión iterativa + leader lines ultradelgadas."""
    cents = gdf.geometry.centroid
    textos = []
    for (_, row), c in zip(gdf.iterrows(), cents):
        textos.append(ax.text(c.x, c.y, str(row[campo_texto]),
                              fontsize=7, ha="center", va="center"))
    adjust_text(
        textos, ax=ax,
        expand_text=(1.2, 1.4), expand_points=(1.2, 1.4),
        force_text=(0.4, 0.6), force_points=(0.3, 0.5),
        arrowprops=dict(arrowstyle="-", color="0.4", lw=0.35),  # leader line 0.2–0.5 pt
        only_move={"text": "xy"},
    )
    return textos
```

Regla: en zonas saturadas (centro consolidado), desplazar el texto fuera del
punto crítico y trazar una línea de llamada de 0.2–0.5 pt que preserva el
anclaje espacial. Fuerza de atracción a la coordenada real, repulsión a textos
vecinos y bordes.
