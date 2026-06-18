# Módulo 1b — MAUP, tinta proporcional y alternativas a la coropleta

Openshaw (MAUP), Goodchild, Wilke (proportional ink), Tobler (picnofiláctica).

## Diagnóstico MAUP

```python
import numpy as np

def heterogeneidad_areal(gdf):
    """Si las áreas son muy heterogéneas, la coropleta clásica miente."""
    a = gdf.geometry.area
    cv = a.std() / a.mean()          # coeficiente de variación del área
    ratio = a.max() / a.min()
    riesgo = cv > 1.0 or ratio > 50
    if riesgo:
        print(f"CV_área={cv:.2f} ratio={ratio:.0f} → riesgo MAUP/tinta desproporcionada.")
        print("Recomendado: cartograma, malla H3 o superficie picnofiláctica.")
    return {"cv": cv, "ratio": ratio, "riesgo_maup": riesgo}
```

Efecto de **escala**: agregar a unidades más grandes cambia el estadístico.
Efecto de **zonificación**: cambiar la forma de los límites con igual n cambia el
resultado. Documentar la unidad de análisis elegida y, si se puede, probar
sensibilidad a otra zonificación.

## Alternativa 1 — Malla H3 (densidades puras)

```python
import h3, geopandas as gpd
from collections import defaultdict
from shapely.geometry import Polygon

def a_hexagonos(gdf_puntos, resolution=9, valor=None):
    """Agrega puntos a celdas H3 homogéneas (elimina sesgo de límites políticos)."""
    g = gdf_puntos.to_crs(4326)
    acc = defaultdict(list)
    for geom, val in zip(g.geometry, (g[valor] if valor else [1]*len(g))):
        h = h3.latlng_to_cell(geom.y, geom.x, resolution)
        acc[h].append(val)
    filas = []
    for h, vals in acc.items():
        ring = h3.cell_to_boundary(h)
        poly = Polygon([(lng, lat) for lat, lng in ring])
        filas.append({"h3": h, "valor": np.mean(vals), "n": len(vals), "geometry": poly})
    return gpd.GeoDataFrame(filas, crs=4326).to_crs(gdf_puntos.crs)
```

## Alternativa 2 — Cartograma

```python
# Cartograma por área proporcional a una variable de control (transacciones, población).
# Opciones: 'geoplot.cartogram' (escala centroides) o el algoritmo de Dorling.
import geoplot as gplt
def cartograma(gdf, scale_col):
    return gplt.cartogram(gdf, scale=scale_col, limits=(0.2, 1.0))
```

## Alternativa 3 — Superficie picnofiláctica de Tobler

```python
# Conserva la masa total por polígono al suavizar (paquete `tobler`).
from tobler.pycno import pycno_interpolate

def superficie_picnofilactica(gdf, valor_col, cellsize=100):
    """Gradiente continuo matemáticamente fiel al dato catastral/censal."""
    return pycno_interpolate(gdf, valor_col, cellsize=cellsize)  # devuelve raster/grid
```

Regla: cuando las áreas son heterogéneas, preferir H3 (comparabilidad), cartograma
(masa visual = peso del dato) o picnofiláctica (gradiente que conserva la masa).
La coropleta clásica queda como última opción y siempre con nota metodológica.
