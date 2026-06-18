---
name: clustering-no-supervisado
description: >
  Use this skill when the user wants to identify spatial clusters, group
  observations by proximity or attribute similarity, or detect geographic
  patterns WITHOUT pre-defined labels — triggers include "clustering espacial",
  "DBSCAN", "HDBSCAN", "OPTICS", "K-means espacial", "agrupamiento de puntos",
  "detectar grupos geográficos", "clústeres de alta densidad", "noise points",
  "core samples", "reachability plot", "dendrograma espacial", "clustering
  jerárquico espacial", "SKATER", "regionalizacion", "spatially constrained
  clustering", "cuántos grupos hay", "segmentar el espacio", "agrupamiento
  sin supervisión". Covers density-based clustering (DBSCAN / HDBSCAN / OPTICS),
  centroid-based spatial K-means, spatially constrained regionalization (SKATER),
  membership probability surfaces, and reachability plot diagnosis.
  Produces reproducible Python (scikit-learn, hdbscan, libpysal).
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill — Clustering espacial no supervisado

Motor de segmentación geográfica. Agrupa observaciones sin etiquetas predefinidas
usando densidad, distancia o contigüidad espacial. Detecta cuántos grupos emergen
de los datos, los separa del ruido y produce geometrías de cluster auditables.

## Principio operativo

No fijar el número de clusters por defecto. Explorar el espacio de parámetros
(`eps`, `min_samples`, `k`) y seleccionar con criterios de calidad internos
(silhouette, ADCM, reachability). Separar siempre ruido de clusters genuinos.
Reportar el tamaño, densidad y coherencia geográfica de cada grupo.

## Orden de ejecución

```
[Dataset + CRS auditados (Skill eda-esda-diagnostico)]
   → [Seleccionar algoritmo según la pregunta]
   → [Optimizar parámetros con criterio interno]
   → [Asignar etiquetas y separar ruido]
   → [Mapear y caracterizar cada cluster]
   → [Auditar coherencia espacial]
```

## Módulo 1 — Selección de algoritmo

| Pregunta | Algoritmo preferido |
|----------|---------------------|
| ¿Hay grupos de densidad variable? ¿Cuántos no sé? | HDBSCAN |
| ¿Grupos compactos de densidad parecida? ¿Cuántos no sé? | DBSCAN |
| ¿Quiero ver el gradiente de densidad (diagrama de reachability)? | OPTICS |
| ¿Sé cuántos grupos quiero? ¿Son razonablemente esféricos? | K-means espacial |
| ¿Los grupos deben respetar la contigüidad administrativa? | SKATER / REDCAP |
| ¿Quiero probabilidades de pertenencia, no asignaciones duras? | HDBSCAN (soft) |

Código: `references/modulo1-seleccion-algoritmo.md`.

## Módulo 2 — Clustering por densidad (DBSCAN / HDBSCAN / OPTICS)

**DBSCAN** (`sklearn.cluster.DBSCAN`):
- Parámetros: `eps` (radio de vecindad), `min_samples` (mínimo de puntos para core).
- Salida: etiqueta `-1` = ruido; ≥ 0 = cluster.
- Calibrar `eps` con el codo de la curva de distancia al k-ésimo vecino.
- Limitación: densidad uniforme asumida; falla si los clusters tienen densidades
  muy distintas.

**HDBSCAN** (`hdbscan.HDBSCAN`):
- Parámetro principal: `min_cluster_size`.
- Produce: etiquetas duras + probabilidades de pertenencia (`probabilities_`).
- Maneja clusters de densidad variable; superior a DBSCAN en datasets heterogéneos.
- Usar `prediction_data=True` para clasificar nuevos puntos.

**OPTICS** (`sklearn.cluster.OPTICS`):
- Produce el diagrama de reachability: valles = clusters, picos = fronteras.
- Útil para VISUALIZAR la estructura jerárquica antes de fijar parámetros.
- `min_samples` es el único parámetro crítico; `max_eps` controla el radio máximo.

Código: `references/modulo2-dbscan-hdbscan-optics.md`.

## Módulo 3 — K-means espacial y regionalización

**K-means espacial** (`sklearn.cluster.KMeans` sobre coordenadas proyectadas):
- Requiere k predefinido; elegir con Elbow + Silhouette Score.
- PROHIBIR K-means sobre coordenadas geográficas (EPSG:4326): distancias en
  grados no son euclídeas.
- Opcionalmente incluir atributos no espaciales escalados: `[x, y, attr1, attr2]`.

**SKATER (Spatial K-means con restricción de contigüidad)** (`libpysal.cg.skater`):
- Los clusters resultantes son CONTIGUOS geográficamente (cada cluster es un
  polígono coherente, no puntos dispersos).
- Requiere W de contigüidad (queen/rook) construida sobre los polígonos de la
  unidad de análisis.
- Útil para regionalización administrativa: fusionar manzanas en zonas, distritos
  en regiones.
- Parámetro: `n_clusters` (número de regiones destino).

**Selección de k:**
- Elbow (inercia vs. k): buscar el codo donde la reducción marginal de inercia
  se aplana.
- Silhouette Score ∈ [−1, 1]: preferir k donde el score sea máximo y > 0.4.

Código: `references/modulo3-kmeans-skater.md`.

## Módulo 4 — Validación y caracterización de clusters

**Métricas internas de validación:**

| Métrica | Rango | Interpretar |
|---------|-------|-------------|
| Silhouette Score | −1 a 1 | > 0.5 = buena separación |
| Davies-Bouldin | 0 a ∞ | Menor = mejor; < 1.5 aceptable |
| Calinski-Harabasz | 0 a ∞ | Mayor = más compacto y separado |

**Auditoría de coherencia espacial:**
- Calcular Moran's I de las etiquetas de cluster: debe ser positivo y significativo.
  Si no lo es, los "clusters" son estadísticamente aleatorios en el espacio.
- Mapear el grafo de conectividad sobre los clusters: ¿se rompen fronteras físicas
  reales? ¿hay islas? Alertar y re-calibrar si es el caso.

**Caracterización por cluster:**
- Estadísticos descriptivos de cada variable dentro del cluster.
- Perfil de atributos: radar chart o heatmap de z-scores por cluster para detectar
  qué variables distinguen cada grupo.
- Tamaño y área (para polígonos): alertar si un cluster tiene < 5 unidades.

Código: `references/modulo4-validacion-clusters.md`.

## Reglas transversales

- PROHIBIR reportar clusters sin métricas de validación interna.
- NUNCA interpretar ruido (label −1) como un cluster con significado sustantivo.
- Cuando HDBSCAN asigne probabilidad de pertenencia < 0.5 a más del 20% de las
  observaciones, reportar como señal de que los clusters no son robustos.
- Los clusters identificados son descriptivos/exploratorios; para inferencia causal
  usar el skill `eda-esda-diagnostico` (LISA, Gi*) o `econometria-hedonica-espacial`.

## Entrega final

Para cada solución de clustering entregar:
1. Mapa de etiquetas con ruido diferenciado (gris claro, sin color temático).
2. Tabla de caracterización por cluster (media/mediana de variables clave).
3. Métrica de validación interna con el parámetro óptimo seleccionado.
4. Moran's I de las etiquetas (prueba de coherencia espacial).
Pasar las figuras al skill `composicion-lienzo-cartografico` para acabado y export.
