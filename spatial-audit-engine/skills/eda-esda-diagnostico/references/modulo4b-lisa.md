# Módulo 4b — LISA (Moran local) e insumo para la máscara

```python
import numpy as np
from esda.moran import Moran_Local
from splot.esda import moran_scatterplot, lisa_cluster

def lisa(y, w, permutations=999, alpha=0.05):
    w.transform = "r"
    lm = Moran_Local(y, w, permutations=permutations)
    # cuadrantes esda: 1=HH, 2=LH, 3=LL, 4=HL
    sig = lm.p_sim < alpha
    cuad_sig = np.where(sig, lm.q, 0)   # 0 = no significativo
    etiquetas = {0: "ns", 1: "HH", 2: "LH", 3: "LL", 4: "HL"}
    print({etiquetas[k]: int((cuad_sig == k).sum()) for k in etiquetas})
    return lm, cuad_sig

def plot_lisa(gdf, y, w, lm):
    moran_scatterplot(lm, p=0.05)          # dispersión local con cuadrantes
    lisa_cluster(lm, gdf, p=0.05)          # mapa de clusters HH/LL/HL/LH

def adjuntar_a_gdf(gdf, cuad_sig):
    """Deja el cuadrante significativo en el GeoDataFrame para el Skill 2."""
    gdf = gdf.copy()
    mapa = {0: "ns", 1: "HH", 2: "LH", 3: "LL", 4: "HL"}
    gdf["lisa"] = [mapa[c] for c in cuad_sig]
    return gdf
```

Reglas: retener solo cuadrantes con `p_sim < 0.05`; lo demás es "ns" (no
significativo) y NO se discute como cluster. HH/LL = clusters de concentración;
HL/LH = outliers espaciales (anomalías). Esta columna `lisa` viaja al Skill 2
como insumo de la máscara de significancia local.

## Corrección por pruebas múltiples (Wheeler)

Con n manzanas se corren n pruebas LISA: ~5% darán significativas por azar.
Aplicar FDR/Bonferroni sobre `lm.p_sim` (ver Skill 2,
`modulo3-mascara-significancia.md::correccion_multiple`) antes de pintar
clústeres. `esda.Moran_Local` también acepta el ajuste FDR vía `seed`/utilidades
de `esda` recientes; como mínimo, reportar cuántos clústeres sobreviven la
corrección.

## Calidad de la partición: gráfico de silueta (Rousseeuw)

No asumir que la segmentación LISA es perfecta. Validar qué tan nítidos son los
límites de los clústeres.

```python
import numpy as np
from sklearn.metrics import silhouette_samples

def silueta_lisa(coords, etiquetas_cuadrante):
    """
    Silueta combinando distancia geográfica y pertenencia de cluster.
    Valor ~1: manzana bien ubicada en su submercado; ~0: frontera ambigua.
    """
    sil = silhouette_samples(coords, etiquetas_cuadrante)
    for q in np.unique(etiquetas_cuadrante):
        print(f"cuadrante {q}: silueta media={sil[etiquetas_cuadrante==q].mean():.2f}")
    return sil   # mapear junto al LISA: zonas de baja silueta = transición difusa
```

Mostrar la silueta junto al mapa LISA demuestra control de la heterogeneidad: las
manzanas de silueta cercana a 0 están en la frontera entre submercados.
