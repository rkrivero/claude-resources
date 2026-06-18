# Módulo 1 — Selección de algoritmo de clustering

## Árbol de decisión

```
¿Sé cuántos clusters quiero?
├── Sí
│   ├── ¿Deben ser contiguos geográficamente? → SKATER
│   └── ¿No importa la contigüidad? → K-means espacial
└── No
    ├── ¿Quiero visualizar la jerarquía de densidad primero? → OPTICS
    ├── ¿Los clusters tienen densidad variable o tamaño diferente? → HDBSCAN
    └── ¿Densidad relativamente uniforme? → DBSCAN
```

## Diagnóstico previo (antes de elegir)

```python
import numpy as np
import geopandas as gpd

def diagnostico_previo(gdf: gpd.GeoDataFrame) -> dict:
    """
    Informa sobre las características del dataset que guían la elección de algoritmo.
    """
    n = len(gdf)
    tipo_geom = gdf.geometry.geom_type.iloc[0]

    # Densidad: ratio de área mínima vs. máxima (indica si el espacio es heterogéneo)
    if tipo_geom in ("Polygon", "MultiPolygon"):
        areas = gdf.geometry.area
        ratio_area = areas.max() / areas.min()
    else:
        ratio_area = None

    # Distribución de distancias (indica si hay clusters naturales)
    from sklearn.neighbors import NearestNeighbors
    coords = np.column_stack([gdf.geometry.centroid.x, gdf.geometry.centroid.y])
    nbrs = NearestNeighbors(n_neighbors=min(5, n-1)).fit(coords)
    dists, _ = nbrs.kneighbors(coords)
    dist_5nn = dists[:, -1]

    print(f"N observaciones: {n}")
    print(f"Tipo de geometría: {tipo_geom}")
    print(f"Distancia al 5-NN: mediana={np.median(dist_5nn):.1f}, "
          f"p95={np.percentile(dist_5nn, 95):.1f} (en unidades del CRS)")
    if ratio_area:
        print(f"Ratio área max/min: {ratio_area:.1f} "
              + ("(alta heterogeneidad → cuidado con K-means)" if ratio_area > 10 else ""))

    return {
        "n": n,
        "tipo_geometria": tipo_geom,
        "dist_5nn_mediana": np.median(dist_5nn),
        "dist_5nn_p95": np.percentile(dist_5nn, 95),
        "ratio_area": ratio_area
    }
```

## Guía de parámetros iniciales por tamaño de dataset

| N observaciones | DBSCAN eps candidato | HDBSCAN min_cluster_size | K-means k inicial |
|----------------|----------------------|--------------------------|-------------------|
| < 100 | p95 de dist 5-NN | max(5, N//10) | 3-5 |
| 100 – 1000 | p90 de dist 5-NN | max(10, N//20) | explorar 3-10 |
| 1000 – 10000 | p85 de dist 5-NN | max(20, N//50) | explorar 5-15 |
| > 10000 | p80 de dist 5-NN | max(50, N//100) | explorar 8-20 |

## Bandera de CRS

PROHIBIDO ejecutar cualquier clustering sobre EPSG:4326 (grados decimales).
Verificar antes:

```python
assert gdf.crs.is_projected, (
    f"CRS {gdf.crs.to_epsg()} no es métrico. "
    "Reproyectar antes de calcular distancias para clustering."
)
```
