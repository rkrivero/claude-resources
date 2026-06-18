# Módulo 1 — Adecuación de la muestra (N variable), óptimos y obtención

El N crece con el tiempo. Este diagnóstico se re-corre cada vez que la muestra
aumenta y reporta: viabilidad computacional, suficiencia para calibración local,
cobertura espacial y la ruta para conseguir/rellenar datos.

## Diagnóstico de adecuación (agnóstico a N)

```python
import numpy as np
from scipy.spatial import cKDTree

def diagnostico_muestra(coords, n_predictores, area_estudio_m2, k_local=None):
    """
    coords: (n,2) en CRS métrico. n_predictores: nº de X del modelo.
    Devuelve un veredicto por dimensión: computacional, local, cobertura.
    """
    n = len(coords)
    rep = {}

    # 1) Viabilidad computacional GWR/MGWR
    if n < 2000:      rep["computacional"] = "ÓPTIMO para GWR/MGWR (corre fluido)"
    elif n < 8000:    rep["computacional"] = "VIABLE (MGWR más lento; considerar paralelizar)"
    else:             rep["computacional"] = "PESADO → ESF o submuestreo (ver control-sesgos)"

    # 2) Suficiencia para calibración
    #    global OLS: >=10-15 por predictor; local: ventana >=5-10 por predictor
    min_global = 10 * n_predictores
    rep["global_ok"] = n >= min_global
    k_sug = max(int(np.ceil(8 * n_predictores)), 30)   # k mínimo por ventana local
    rep["k_local_sugerido"] = min(k_sug, n - 1)
    rep["local_ok"] = n >= rep["k_local_sugerido"] + n_predictores

    # 3) Cobertura espacial
    tree = cKDTree(coords)
    d_nn, _ = tree.query(coords, k=2)
    d_obs = d_nn[:, 1].mean()
    d_exp = 0.5 / np.sqrt(n / area_estudio_m2)
    nni = d_obs / d_exp                                 # <1 agrupado, ~1 aleatorio, >1 disperso
    densidad = n / (area_estudio_m2 / 1e6)              # puntos por km²
    rep["NNI"] = round(nni, 3)
    rep["densidad_pts_km2"] = round(densidad, 2)

    print(rep)
    return rep

def cobertura_area(coords, area_estudio_geom, d=300):
    """% del área de estudio que está a < d metros de algún punto (buffer union)."""
    import geopandas as gpd
    from shapely.geometry import Point
    bufs = gpd.GeoSeries([Point(xy).buffer(d) for xy in coords]).union_all()
    cob = bufs.intersection(area_estudio_geom).area / area_estudio_geom.area
    print(f"Cobertura a <{d} m: {cob:.1%} del área de estudio")
    return cob
```

## Rangos óptimos / reglas de oro

| Dimensión | Regla | Fuente conceptual |
|-----------|-------|-------------------|
| Computacional GWR/MGWR | cientos–pocos miles; >~8k usar ESF | Fotheringham; PySAL |
| Global (OLS hedónico) | ≥10–15 obs por predictor | práctica econométrica |
| Local (ventana de bw) | ≥5–10 obs por parámetro por ventana | GWR adaptativo |
| Ancho de banda adaptativo | fijar k mínimo ≈ 8×nº params (≥30) | Fotheringham |
| Cobertura | NNI no extremo + sin vacíos por zona | Anselin |

Estos son criterios de orientación, no umbrales sagrados: documentar el usado.

## Dónde y cómo obtener / crecer la muestra

- **Portales inmobiliarios** (scraping periódico): Urbania, Adondevivir,
  Properati, marketplaces locales. Programar scraping incremental (ver skill
  `entorno-pipeline-reproducible`) y deduplicar por geometría + precio + fecha.
- **Registros públicos**: SUNARP (transferencias), municipalidades (catastro,
  licencias), tasaciones, notarías. Más fiables que anuncios (precio real vs.
  pedido); declarar la fuente y el tipo de precio.
- **Trabajo de campo dirigido**: levantar puntos solo donde el diagnóstico muestra
  vacíos.

## Muestreo dirigido para llenar vacíos (cobertura > cantidad)

```python
import h3, numpy as np

def celdas_subrepresentadas(coords_wgs84, resolution=8, umbral=2):
    """Detecta celdas H3 con pocos o cero puntos → objetivos de recolección."""
    from collections import Counter
    cnt = Counter(h3.latlng_to_cell(lat, lng, resolution) for lng, lat in coords_wgs84)
    # celdas del área de estudio con conteo < umbral son prioridad de muestreo
    objetivos = [c for c, n in cnt.items() if n < umbral]
    print(f"{len(objetivos)} celdas H3-{resolution} subrepresentadas (priorizar recolección)")
    return objetivos
```

Regla: priorizar la recolección en las celdas subrepresentadas (muestreo
espacialmente estratificado) en lugar de acumular más puntos donde ya sobran.

## Monitor de crecimiento

Re-correr `diagnostico_muestra` cada vez que entren datos nuevos. Al subir N:
recalcular el k adaptativo óptimo, reevaluar la viabilidad computacional y
actualizar el mapa de cobertura/vacíos. Versionar el reporte (fecha, N, NNI,
cobertura) para mostrar la evolución en la tesis.

## Reencuadre ante el jurado

No discutir "¿N es poco?". Mostrar: viabilidad computacional + suficiencia por
ventana local + mapa de cobertura sin vacíos críticos. Reencuadrar a cómo la forma
urbana y la topografía alteran los gradientes locales. Declarar honestamente las
zonas sin muestra como limitación y restringir conclusiones a las áreas cubiertas.
