# Módulo 1d — Estadística espacial central y de dispersión

Describe la distribución geográfica de un conjunto de puntos o centroides de polígonos
mediante medidas de tendencia central y dispersión espacial. Opera antes de construir W
o calcular autocorrelación: caracteriza dónde está la masa del fenómeno y cuánto se dispersa.

## Centro medio y mediano

```python
import numpy as np
import geopandas as gpd
from shapely.geometry import Point

def centro_medio(gdf: gpd.GeoDataFrame, peso: str | None = None) -> Point:
    """Centro medio (ponderado si se indica peso)."""
    x = gdf.geometry.x
    y = gdf.geometry.y
    if peso:
        w = gdf[peso]
        return Point(np.average(x, weights=w), np.average(y, weights=w))
    return Point(x.mean(), y.mean())

def centro_mediano(gdf: gpd.GeoDataFrame, n_iter: int = 100) -> Point:
    """Centro mediano espacial (minimiza distancias absolutas; algoritmo de Weiszfeld)."""
    x, y = gdf.geometry.x.values, gdf.geometry.y.values
    cx, cy = x.mean(), y.mean()
    for _ in range(n_iter):
        d = np.hypot(x - cx, y - cy).clip(min=1e-10)
        cx = (x / d).sum() / (1 / d).sum()
        cy = (y / d).sum() / (1 / d).sum()
    return Point(cx, cy)
```

## Elipse de desviación estándar (SDE)

La SDE captura la extensión y orientación dominante de una distribución de puntos.
Parámetros: semi-eje mayor `a`, semi-eje menor `b`, ángulo de rotación `θ`.

```python
from shapely.affinity import rotate, scale
from shapely.geometry import Point
import numpy as np

def elipse_desviacion_estandar(
    gdf: gpd.GeoDataFrame,
    peso: str | None = None,
    n_sd: float = 1.0
) -> gpd.GeoDataFrame:
    """
    Calcula la elipse de desviación estándar de orden n_sd.
    Devuelve GeoDataFrame con la elipse como polígono y los parámetros:
      'semi_a', 'semi_b', 'angulo_grados'
    """
    x = gdf.geometry.x.values
    y = gdf.geometry.y.values
    if peso and peso in gdf.columns:
        w = gdf[peso].values
    else:
        w = np.ones(len(x))
    w = w / w.sum()

    cx = np.sum(w * x)
    cy = np.sum(w * y)
    dx = x - cx
    dy = y - cy

    # Ángulo de rotación (eje con mayor varianza)
    num = 2 * np.sum(w * dx * dy)
    denom = np.sum(w * dx**2) - np.sum(w * dy**2)
    theta = 0.5 * np.arctan2(num, denom)

    cos_t, sin_t = np.cos(theta), np.sin(theta)
    dx_r = cos_t * dx + sin_t * dy
    dy_r = -sin_t * dx + cos_t * dy

    n = len(x)
    semi_a = n_sd * np.sqrt(np.sum(w * dx_r**2) * n)
    semi_b = n_sd * np.sqrt(np.sum(w * dy_r**2) * n)

    # Construir elipse unitaria y transformar
    theta_arr = np.linspace(0, 2 * np.pi, 360)
    ex = semi_a * np.cos(theta_arr)
    ey = semi_b * np.sin(theta_arr)
    angle_deg = np.degrees(theta)
    ex_r = cos_t * ex - sin_t * ey + cx
    ey_r = sin_t * ex + cos_t * ey + cy

    from shapely.geometry import Polygon
    elipse_poly = Polygon(zip(ex_r, ey_r))
    resultado = gpd.GeoDataFrame(
        {"semi_a": [semi_a], "semi_b": [semi_b], "angulo_grados": [angle_deg]},
        geometry=[elipse_poly],
        crs=gdf.crs
    )
    return resultado

def comparar_sde(gdfs: dict[str, gpd.GeoDataFrame], peso: str | None = None):
    """
    Compara SDEs de varios grupos o períodos temporales.
    gdfs: {'grupo_A': gdf_A, 'grupo_B': gdf_B, ...}
    """
    resultados = []
    for nombre, gdf in gdfs.items():
        sde = elipse_desviacion_estandar(gdf, peso=peso)
        sde["grupo"] = nombre
        resultados.append(sde)
    return gpd.GeoDataFrame(pd.concat(resultados, ignore_index=True), crs=list(gdfs.values())[0].crs)
```

## Media direccional

Para datos de dirección (ángulos, rumbos de flujos migratorios, orientación de calles):

```python
def media_direccional(angulos_grados: np.ndarray) -> dict:
    """
    Media y dispersión circular de un vector de ángulos en grados.
    Devuelve: mean_angle, r (longitud del vector resultante, 0–1), circular_std.
    """
    rad = np.radians(angulos_grados)
    C = np.cos(rad).mean()
    S = np.sin(rad).mean()
    r = np.hypot(C, S)
    mean_angle = np.degrees(np.arctan2(S, C)) % 360
    circular_std = np.degrees(np.sqrt(-2 * np.log(r)))
    return {"media_grados": mean_angle, "r": r, "desviacion_circular": circular_std}
```

## Cuándo usar

| Medida | Cuándo aplicar |
|--------|---------------|
| Centro medio | Comparar centroides entre grupos o períodos; rápido, sensible a valores extremos |
| Centro mediano | Cuando hay outliers geográficos o distribución asimétrica |
| SDE (1 SD) | ~68% de las observaciones; dirección dominante del fenómeno |
| SDE (2 SD) | ~95%; área de influencia máxima |
| SDE comparativa | Detectar cambios de localización y dispersión entre períodos o grupos |
| Media direccional | Flujos, migración, orientación de vías, vectores de viento |

## Interpretación de la SDE

- `semi_a` >> `semi_b`: distribución muy elongada (eje lineal dominante — corredor, eje vial, valle)
- `semi_a` ≈ `semi_b`: distribución relativamente circular (concentración omnidireccional)
- `angulo_grados`: orientación del eje mayor (0° = este-oeste, 90° = norte-sur)
- SDE que se desplaza entre períodos: migración del centro de gravedad del fenómeno

## Antipatrones

- No usar SDE con < 30 observaciones (los ejes son inestables).
- Nunca reportar la SDE sin el centro medio como referencia.
- No confundir SDE con buffer de distancia: la SDE no garantiza que el X% de
  puntos esté dentro (eso depende de la distribución real, no de la normal).
