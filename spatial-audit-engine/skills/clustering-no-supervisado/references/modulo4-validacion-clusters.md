# Módulo 4 — Validación y caracterización de clusters espaciales

## Métricas internas de validación

```python
import numpy as np
import pandas as pd
import geopandas as gpd
from sklearn.metrics import silhouette_score, davies_bouldin_score, calinski_harabasz_score

def validar_clustering(X: np.ndarray, labels: np.ndarray) -> dict:
    """
    X: features usados para el clustering (escalados).
    labels: array de etiquetas (sin ruido -1, si existe, filtrar antes).
    """
    mask = labels >= 0  # excluir ruido
    if mask.sum() < 2 or len(set(labels[mask])) < 2:
        print("Insuficientes clusters o muestras para métricas de validación.")
        return {}

    X_clean = X[mask]
    l_clean = labels[mask]

    sil = silhouette_score(X_clean, l_clean)
    db  = davies_bouldin_score(X_clean, l_clean)
    ch  = calinski_harabasz_score(X_clean, l_clean)

    print(f"Silhouette Score:    {sil:.3f}  (> 0.5 = buena separación)")
    print(f"Davies-Bouldin:      {db:.3f}  (< 1.5 = aceptable)")
    print(f"Calinski-Harabasz:   {ch:.1f}  (mayor = más compacto)")
    return {"silhouette": sil, "davies_bouldin": db, "calinski_harabasz": ch}
```

## Auditoría de coherencia espacial (Moran sobre etiquetas)

```python
from libpysal.weights import Queen
from esda.moran import Moran
import geopandas as gpd
import numpy as np

def moran_coherencia_clusters(
    gdf: gpd.GeoDataFrame,
    col_etiqueta: str,
    permutations: int = 999
) -> float:
    """
    Calcula Moran's I sobre las etiquetas de cluster.
    Un Moran positivo y significativo confirma que los clusters tienen
    coherencia espacial (observaciones similares están agrupadas).
    """
    w = Queen.from_dataframe(gdf, silence_warnings=True)
    w.transform = "r"
    # Excluir ruido (etiqueta -1) para el cálculo
    mask = gdf[col_etiqueta] >= 0
    if mask.sum() < 10:
        print("Muy pocas observaciones no-ruido para calcular Moran.")
        return None
    labels_clean = gdf.loc[mask, col_etiqueta].values.astype(float)
    w_sub = w  # idealmente subset del peso, simplificado aquí
    m = Moran(gdf[col_etiqueta].values.astype(float), w, permutations=permutations)
    print(f"Moran's I (etiquetas de cluster) = {m.I:.4f}, p = {m.p_sim:.4f}")
    if m.p_sim > 0.05:
        print("ADVERTENCIA: Moran no significativo → los clusters no tienen coherencia espacial.")
    return m.I
```

## Caracterización por cluster

```python
def caracterizar_clusters(
    gdf: gpd.GeoDataFrame,
    col_etiqueta: str,
    variables: list[str]
) -> pd.DataFrame:
    """
    Estadísticos descriptivos por cluster: media, mediana, DE, n.
    El cluster -1 (ruido) se incluye por transparencia pero se etiqueta como 'Ruido'.
    """
    gdf_tmp = gdf.copy()
    gdf_tmp["_etiq"] = gdf_tmp[col_etiqueta].map(
        lambda x: "Ruido" if x == -1 else f"Cluster {x}"
    )
    stats = gdf_tmp.groupby("_etiq")[variables].agg(["mean", "median", "std", "count"])
    stats.columns = ["_".join(c) for c in stats.columns]
    return stats.round(3)
```

## Perfil visual: heatmap de z-scores por cluster

```python
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import StandardScaler

def heatmap_perfil_clusters(
    gdf: gpd.GeoDataFrame,
    col_etiqueta: str,
    variables: list[str]
) -> None:
    """
    Heatmap de z-scores medios por cluster.
    Revela qué variables caracterizan a cada grupo.
    """
    mask = gdf[col_etiqueta] >= 0
    df = gdf.loc[mask].copy()
    X = StandardScaler().fit_transform(df[variables])
    df_z = pd.DataFrame(X, columns=variables, index=df.index)
    df_z["cluster"] = df[col_etiqueta].values
    perfil = df_z.groupby("cluster")[variables].mean()

    fig, ax = plt.subplots(figsize=(max(6, len(variables)), max(4, len(perfil) * 0.6)))
    sns.heatmap(
        perfil, annot=True, fmt=".2f", cmap="RdBu_r", center=0,
        linewidths=0.5, ax=ax
    )
    ax.set_title("Perfil de z-scores por cluster")
    ax.set_xlabel("Variable")
    ax.set_ylabel("Cluster")
    plt.tight_layout()
```

## Checklist de validación antes de reportar

☐ Silhouette Score > 0.3 (mínimo aceptable); > 0.5 = buena separación  
☐ Moran's I de etiquetas positivo y p < 0.05 (coherencia espacial)  
☐ Ratio de ruido < 30% (si > 30%, revisar parámetros)  
☐ No hay cluster con < 5 observaciones (a menos que el tamaño esté justificado)  
☐ Caracterización reportada (tabla de medias por cluster)  
☐ Mapa con ruido diferenciado (gris, sin color temático)
