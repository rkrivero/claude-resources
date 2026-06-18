# Módulo 3 — K-means espacial y regionalización con SKATER

## K-means espacial

```python
import numpy as np
import geopandas as gpd
from sklearn.cluster import KMeans
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import silhouette_score, davies_bouldin_score
import matplotlib.pyplot as plt

def kmeans_espacial(
    gdf: gpd.GeoDataFrame,
    k: int,
    atributos: list[str] | None = None,
    peso_coords: float = 1.0,
    col_etiqueta: str = "cluster_kmeans",
    seed: int = 42
) -> gpd.GeoDataFrame:
    """
    K-means con componente espacial (coordenadas) más atributos opcionales.
    peso_coords: multiplica coordenadas normalizadas para controlar cuánto
                 pesa la proximidad geográfica vs. los atributos.
    Requiere CRS métrico (no EPSG:4326).
    """
    scaler = StandardScaler()
    coords = scaler.fit_transform(
        np.column_stack([gdf.geometry.x, gdf.geometry.y])
    ) * peso_coords

    if atributos:
        attrs = scaler.fit_transform(gdf[atributos].values)
        X = np.hstack([coords, attrs])
    else:
        X = coords

    km = KMeans(n_clusters=k, random_state=seed, n_init=10)
    labels = km.fit_predict(X)
    gdf = gdf.copy()
    gdf[col_etiqueta] = labels
    sil = silhouette_score(X, labels)
    db = davies_bouldin_score(X, labels)
    print(f"k={k}: Silhouette={sil:.3f}, Davies-Bouldin={db:.3f}")
    return gdf


def elbow_silhouette(
    gdf: gpd.GeoDataFrame,
    k_range: range = range(2, 11),
    atributos: list[str] | None = None,
    seed: int = 42
) -> dict:
    """Explora rango de k y devuelve inercia y silhouette para cada k."""
    scaler = StandardScaler()
    coords = scaler.fit_transform(np.column_stack([gdf.geometry.x, gdf.geometry.y]))
    if atributos:
        attrs = scaler.fit_transform(gdf[atributos].values)
        X = np.hstack([coords, attrs])
    else:
        X = coords

    inercias, silhouettes = [], []
    for k in k_range:
        km = KMeans(n_clusters=k, random_state=seed, n_init=10).fit(X)
        inercias.append(km.inertia_)
        silhouettes.append(silhouette_score(X, km.labels_))

    fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 4))
    ax1.plot(k_range, inercias, "o-")
    ax1.set_xlabel("k"); ax1.set_ylabel("Inercia"); ax1.set_title("Elbow")
    ax2.plot(k_range, silhouettes, "o-", color="darkorange")
    ax2.set_xlabel("k"); ax2.set_ylabel("Silhouette"); ax2.set_title("Silhouette")
    plt.tight_layout()

    k_opt = k_range[np.argmax(silhouettes)]
    print(f"k óptimo por silhouette: {k_opt} (score={max(silhouettes):.3f})")
    return {"k_range": list(k_range), "inercias": inercias, "silhouettes": silhouettes}
```

## SKATER — Regionalización con restricción de contigüidad

```python
from libpysal.weights import Queen, Rook
from libpysal.cg.alpha_shapes import alpha_shape_auto
import numpy as np
import geopandas as gpd

def regionalizar_skater(
    gdf: gpd.GeoDataFrame,
    n_clusters: int,
    atributos: list[str],
    tipo_w: str = "queen",
    col_etiqueta: str = "cluster_skater"
) -> gpd.GeoDataFrame:
    """
    SKATER: regionalización espacialmente contigua.
    Los clusters resultantes son polígonos contiguos (no puntos dispersos).
    Requiere GeoDataFrame de polígonos con W de contigüidad.
    """
    from sklearn.preprocessing import StandardScaler
    from libpysal.weights import w_subset
    try:
        from spreg.skater import Skater
    except ImportError:
        from libpysal.cg.skater import Skater

    # Construir W
    if tipo_w == "queen":
        w = Queen.from_dataframe(gdf, silence_warnings=True)
    else:
        w = Rook.from_dataframe(gdf, silence_warnings=True)

    # Verificar que no hay islas
    if w.islands:
        print(f"ADVERTENCIA: {len(w.islands)} islas en W. SKATER puede fallar.")

    # Datos estandarizados
    X = StandardScaler().fit_transform(gdf[atributos].values)

    # Construir árbol mínimo de expansión y cortar
    skater = Skater(w=w, data=X, n_clusters=n_clusters)
    labels = skater.labels_

    gdf = gdf.copy()
    gdf[col_etiqueta] = labels
    print(f"Regiones creadas: {len(set(labels))} con contigüidad {tipo_w}")
    return gdf
```

## Cuándo usar SKATER vs. K-means

| Criterio | K-means espacial | SKATER |
|----------|------------------|--------|
| Los clusters deben ser contiguos | No | Sí (hard constraint) |
| Unidades son polígonos | Puede usarse | Recomendado |
| Unidades son puntos | Preferido | Difícil de implementar |
| Respeta fronteras físicas | No | Solo si W las respeta |
| Interpretación como regiones | Difícil | Natural |
| Velocidad (n > 10,000) | Muy rápido | Más lento |
