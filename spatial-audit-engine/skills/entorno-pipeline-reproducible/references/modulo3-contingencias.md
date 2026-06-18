# Módulo 3 — Planes de contingencia

## Escenario A — MGWR se cuelga / sin memoria → ESF

El backfitting de MGWR es O(N²)–O(N³). Con miles de observaciones satura RAM.
Alternativa: Filtros Espaciales por Autovectores (Eigenvector Spatial Filtering).

```python
import numpy as np
from spreg import OLS

def esf(y, X, w, n_eigvec=None, umbral_mc=0.25):
    """
    Extrae autovectores de la matriz de conectividad como proxies de la
    estructura espacial y los mete en un OLS normal. Captura heterogeneidad
    sin bucles iterativos de GWR.
    """
    w.transform = "B"                 # binaria para el filtrado
    W = w.full()[0]
    n = W.shape[0]
    M = np.eye(n) - np.ones((n, n)) / n
    MWM = M @ W @ M                    # matriz doblemente centrada
    vals, vecs = np.linalg.eigh(MWM)
    orden = np.argsort(vals)[::-1]
    vals, vecs = vals[orden], vecs[:, orden]
    # retener autovectores con autocorrelación relevante (MC alto)
    mc = vals / vals.max()
    sel = vecs[:, mc > umbral_mc]
    if n_eigvec:
        sel = sel[:, :n_eigvec]
    Xe = np.hstack([X, sel])
    return OLS(y, Xe, w=w, spat_diag=True, moran=True), sel.shape[1]
```

Reportar cuántos autovectores entraron y verificar que el Moran de residuos del
ESF ya no sea significativo.

## Escenario B — adjustText congela el render

```python
# Opción 1: etiquetar solo los outliers extremos (no miles de etiquetas)
import mapclassify as mc

def top_outliers(gdf, col, n=10):
    g = gdf.copy()
    g["_rank"] = g[col].rank(ascending=False)
    return g.nlargest(n, col)         # etiquetar solo estos
```

Opción 2 — migrar la exploración a motores interactivos (colisiones delegadas al
navegador):

```python
import folium
from folium.plugins import MarkerCluster

def mapa_interactivo(gdf_wgs84, popup_col):
    m = folium.Map(location=[gdf_wgs84.geometry.y.mean(),
                             gdf_wgs84.geometry.x.mean()], zoom_start=13,
                   tiles="CartoDB positron")
    mc_layer = MarkerCluster().add_to(m)
    for _, r in gdf_wgs84.iterrows():
        folium.Marker([r.geometry.y, r.geometry.x],
                      popup=str(r[popup_col])).add_to(mc_layer)
    return m
# Alternativa equivalente con plotly.express.scatter_mapbox para diagnóstico.
```

## Escenario C — dependency hell / segfault al importar GeoPandas

NO parchar con `pip`. Reinstalar limpio:

```bash
pixi clean            # o: mamba env remove -n cusco-estructura
rm -f pixi.lock
pixi install          # reconstruye desde pixi.toml (canal conda-forge único)
# verificación:
pixi run python -c "import geopandas, libpysal, esda, mgwr, spreg; print('ok')"
```

Causa: binarios C++ del SO desalineados con los wrappers. Un solo canal
(`conda-forge`) descarga todo pre-compilado para entenderse entre sí.
