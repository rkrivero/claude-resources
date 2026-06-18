# Módulo 4 — Fondos contextuales mudos (contextily)

```python
import contextily as cx

def basemap_mudo(ax, gdf, alpha=0.5):
    """Teselas en gris/sin etiquetas, subordinadas al análisis. alpha 0.4–0.6."""
    assert 0.4 <= alpha <= 0.6, "alpha del mapa base debe estar entre 0.4 y 0.6"
    # reproyectar las teselas al CRS de los datos (crs=gdf.crs), no al revés
    cx.add_basemap(
        ax, crs=gdf.crs.to_string(),
        source=cx.providers.CartoDB.PositronNoLabels,   # sin texto, neutro
        alpha=alpha, interpolation="bilinear",
    )
    return ax

# Alternativas mudas válidas:
#   cx.providers.Stamen.TonerBackground
#   cx.providers.CartoDB.DarkMatterNoLabels  (solo para fondos oscuros)
```

Regla: SOLO servidores en escala de grises sin etiquetas para no competir con los
coeficientes. `alpha` estricto 0.4–0.6, interpolación bilineal al reescalar. Sirve
para integrar hillshading o trama vial como variable de accesibilidad visual,
siempre subordinada.
