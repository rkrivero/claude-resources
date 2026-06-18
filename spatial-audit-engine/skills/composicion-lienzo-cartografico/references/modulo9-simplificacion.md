# Módulo 9 — Simplificación cartográfica (Robinson)

El mapa es abstracción, no foto aérea. El detalle geométrico irrelevante genera
ruido de alta frecuencia que compite con los gradientes econométricos.

```python
import geopandas as gpd

def simplificar(gdf, tolerancia_m=5.0, preservar_topologia=True):
    """Douglas-Peucker vía shapely. tolerancia en unidades del CRS (m en UTM)."""
    g = gdf.copy()
    g["geometry"] = g.geometry.simplify(tolerancia_m, preserve_topology=preservar_topologia)
    return g
```

Reglas:
- Capas base (manzanas, vías) → simplificar para quitar vértices intrincados y
  aligerar el render de `matplotlib`.
- Para evitar huecos/solapes entre polígonos vecinos al simplificar, preferir
  `topojson`/`mapshaper` (simplificación topológica) cuando la tolerancia es alta.
- NO simplificar la capa temática si la precisión del límite importa para el
  análisis; la simplificación es para reducir ruido visual de soporte.
- Tolerancia típica en tejido urbano: 2–10 m (UTM). Ajustar según escala de
  impresión.
