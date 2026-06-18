# Módulo 2 — Clustering por densidad: DBSCAN / HDBSCAN / OPTICS

## DBSCAN

```python
import numpy as np
import geopandas as gpd
from sklearn.cluster import DBSCAN
from sklearn.neighbors import NearestNeighbors
import matplotlib.pyplot as plt

def calibrar_eps(gdf: gpd.GeoDataFrame, k: int = 5) -> float:
    """
    Estima eps óptimo mediante la curva de distancia al k-ésimo vecino.
    El codo de la curva (punto de máxima curvatura) es el eps candidato.
    """
    coords = np.column_stack([gdf.geometry.x, gdf.geometry.y])
    nbrs = NearestNeighbors(n_neighbors=k).fit(coords)
    dist, _ = nbrs.kneighbors(coords)
    dist_k = np.sort(dist[:, -1])[::-1]
    fig, ax = plt.subplots(figsize=(6, 3))
    ax.plot(dist_k)
    ax.set_xlabel("Observaciones (ordenadas)")
    ax.set_ylabel(f"Distancia al {k}-ésimo vecino")
    ax.set_title("Codo para calibrar eps (DBSCAN)")
    plt.tight_layout()
    return dist_k

def dbscan_espacial(
    gdf: gpd.GeoDataFrame,
    eps: float,
    min_samples: int = 5,
    col_etiqueta: str = "cluster_dbscan"
) -> gpd.GeoDataFrame:
    """
    Aplica DBSCAN sobre las coordenadas proyectadas del GeoDataFrame.
    Requiere CRS métrico (no geográfico).
    Label -1 = ruido.
    """
    coords = np.column_stack([gdf.geometry.x, gdf.geometry.y])
    db = DBSCAN(eps=eps, min_samples=min_samples, metric="euclidean")
    gdf = gdf.copy()
    gdf[col_etiqueta] = db.fit_predict(coords)
    n_clusters = len(set(gdf[col_etiqueta])) - (1 if -1 in gdf[col_etiqueta].values else 0)
    n_ruido = (gdf[col_etiqueta] == -1).sum()
    print(f"Clusters: {n_clusters} | Ruido: {n_ruido} ({n_ruido/len(gdf)*100:.1f}%)")
    return gdf
```

## HDBSCAN

```python
import hdbscan

def hdbscan_espacial(
    gdf: gpd.GeoDataFrame,
    min_cluster_size: int = 10,
    min_samples: int | None = None,
    col_etiqueta: str = "cluster_hdbscan"
) -> gpd.GeoDataFrame:
    """
    HDBSCAN: maneja clusters de densidad variable.
    Parámetros:
      min_cluster_size: tamaño mínimo para considerar un cluster.
      min_samples:      None → igual que min_cluster_size (más sensible al ruido si < mcs).
    """
    coords = np.column_stack([gdf.geometry.x, gdf.geometry.y])
    clusterer = hdbscan.HDBSCAN(
        min_cluster_size=min_cluster_size,
        min_samples=min_samples,
        prediction_data=True
    )
    labels = clusterer.fit_predict(coords)
    gdf = gdf.copy()
    gdf[col_etiqueta] = labels
    gdf["prob_pertenencia"] = clusterer.probabilities_
    n_clusters = len(set(labels)) - (1 if -1 in labels else 0)
    n_ruido = (labels == -1).sum()
    print(f"Clusters: {n_clusters} | Ruido: {n_ruido} | Prob. media: {clusterer.probabilities_.mean():.2f}")
    return gdf, clusterer

def advertir_inestabilidad(gdf: gpd.GeoDataFrame, col_prob: str = "prob_pertenencia", umbral: float = 0.5):
    baja_prob = (gdf[col_prob] < umbral).sum()
    ratio = baja_prob / len(gdf)
    if ratio > 0.2:
        print(f"ADVERTENCIA: {ratio*100:.1f}% de observaciones con probabilidad < {umbral}. "
              "Los clusters pueden no ser robustos. Revisar min_cluster_size.")
```

## OPTICS y diagrama de reachability

```python
from sklearn.cluster import OPTICS
import matplotlib.pyplot as plt
import numpy as np

def optics_reachability(
    gdf: gpd.GeoDataFrame,
    min_samples: int = 5,
    max_eps: float = np.inf,
    col_etiqueta: str = "cluster_optics"
) -> gpd.GeoDataFrame:
    """
    OPTICS: produce el diagrama de reachability para exploración estructural.
    """
    coords = np.column_stack([gdf.geometry.x, gdf.geometry.y])
    opt = OPTICS(min_samples=min_samples, max_eps=max_eps)
    opt.fit(coords)

    # Diagrama de reachability
    fig, ax = plt.subplots(figsize=(10, 4))
    space = np.arange(len(coords))
    rd = opt.reachability_[opt.ordering_]
    ax.bar(space, rd, width=1.0, color="steelblue", alpha=0.7)
    ax.set_xlabel("Observaciones (orden OPTICS)")
    ax.set_ylabel("Reachability distance")
    ax.set_title("Diagrama de reachability — valles = clusters")
    plt.tight_layout()

    gdf = gdf.copy()
    gdf[col_etiqueta] = opt.labels_
    return gdf, opt
```

## Reglas de calibración

| Parámetro | Cómo elegir |
|-----------|-------------|
| DBSCAN `eps` | Codo de curva k-NN (k = min_samples) |
| DBSCAN `min_samples` | ≥ dimensiones + 1; para 2D espacial: 5 como punto de partida |
| HDBSCAN `min_cluster_size` | Mínimo de observaciones que tiene sentido sustantivo como grupo |
| OPTICS `min_samples` | Igual que DBSCAN; explorar 3, 5, 10 |

## Comparación rápida de los tres algoritmos

| Característica | DBSCAN | HDBSCAN | OPTICS |
|----------------|--------|---------|--------|
| Densidad variable | No | Sí | Sí (visualiza) |
| Parámetros | eps, min_s | min_cluster_size | min_s |
| Ruido explícito | Sí (−1) | Sí (−1) | Sí (−1) |
| Probabilidades | No | Sí | No |
| Velocidad | Rápido | Moderado | Más lento |
| Recomendado para | Densidad homogénea | Densidad variable | Exploración |
