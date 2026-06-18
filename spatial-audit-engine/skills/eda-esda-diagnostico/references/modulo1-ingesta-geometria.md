# Módulo 1 — Ingesta, perfilado geométrico y control de escala

Código runnable. CRS objetivo: ajustar al CRS métrico local del área de estudio (ejemplo: UTM 19S = EPSG:32719).

```python
import geopandas as gpd
import numpy as np
from libpysal.weights import KNN

TARGET_CRS = "EPSG:32719"  # ejemplo UTM 19S. Cambiar al CRS métrico local según caso de estudio.

def cargar_y_auditar(path):
    gdf = gpd.read_file(path)

    # --- Regla CRS: exigir proyección métrica ---
    if gdf.crs is None:
        raise ValueError("FRENO: el dataset no declara CRS. Definir antes de continuar.")
    if gdf.crs.is_geographic:
        print(f"ALERTA: CRS geográfico ({gdf.crs.to_epsg()}). Reproyectando a {TARGET_CRS}.")
        gdf = gdf.to_crs(TARGET_CRS)
    elif gdf.crs.to_epsg() != 32719:
        print(f"AVISO: CRS proyectado {gdf.crs.to_epsg()} ≠ objetivo. Verificar zona UTM.")

    geom_types = gdf.geom_type.unique()
    return gdf, geom_types

def clasificar_entidad(gdf):
    """object view (puntos/scraping) vs field view (polígonos/agregación)."""
    gt = gdf.geom_type.iloc[0]
    if "Point" in gt:
        return "object"   # entidades discretas, riesgo de cluttering
    return "field"        # unidades de agregación espacial

def diagnostico_densidad_puntos(gdf):
    """Índice de vecino más cercano (NNI). NNI<1 => agrupado; >1 => disperso."""
    coords = np.c_[gdf.geometry.x, gdf.geometry.y]
    w = KNN.from_array(coords, k=1)
    # distancia media observada al vecino más cercano
    from scipy.spatial import cKDTree
    d, _ = cKDTree(coords).query(coords, k=2)
    d_obs = d[:, 1].mean()
    n = len(coords)
    area = gdf.total_bounds  # xmin,ymin,xmax,ymax
    A = (area[2]-area[0]) * (area[3]-area[1])
    d_exp = 0.5 / np.sqrt(n / A)
    nni = d_obs / d_exp
    if nni < 0.6:
        print(f"NNI={nni:.2f}: alta densidad. PROHIBIR marcadores simples → usar H3 o KDE.")
    return nni

def diagnostico_ratio_area(gdf):
    """Sesgo del suelo periférico: polígonos enormes vs diminutos."""
    a = gdf.geometry.area
    ratio = a.max() / a.min()
    if ratio > 50:
        print(f"Ratio área máx/mín={ratio:.0f}: coropleta normal creará 'mentira visual'.")
        print("→ Recomendado: centroides con símbolos proporcionales.")
    return ratio
```

Para agregación a malla hexagonal usar `h3`; para superficie continua, KDE con
`scipy.stats.gaussian_kde` o `seaborn.kdeplot`. Decidir SIEMPRE según NNI/ratio,
no por defecto.
