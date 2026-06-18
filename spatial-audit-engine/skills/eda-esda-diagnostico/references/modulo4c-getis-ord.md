# Módulo 4c — Getis-Ord Gi* (hotspots de concentración absoluta)

Diferencia clave (Getis & Ord): Gi* INCLUYE el valor de la propia unidad `i`
(`star=True`) y mide concentración absoluta; LISA mide similitud con el entorno.

```python
import numpy as np
from esda.getisord import G_Local

def getis_ord(y, w, star=True, permutations=999):
    w.transform = "r"
    g = G_Local(y, w, star=star, permutations=permutations)
    z = g.Zs                      # Z-scores
    p = g.p_sim
    # clasificación por umbrales de la normal al 95%
    clase = np.full(len(z), "ns", dtype=object)
    clase[(z >= 1.96) & (p < 0.05)] = "hotspot"
    clase[(z <= -1.96) & (p < 0.05)] = "coldspot"
    print({c: int((clase == c).sum()) for c in ("hotspot", "coldspot", "ns")})
    return g, z, clase

def adjuntar(gdf, z, clase):
    gdf = gdf.copy()
    gdf["giz"] = z
    gdf["gi_clase"] = clase
    return gdf
```

Mapeo: escala visual = Z-scores con cortes en ±1.96 (y opcional ±2.58 para 99%).
Hotspots saturados en un extremo, coldspots en el opuesto, lo no significativo
en tono neutro desaturado. Para paleta divergente anclada en 0, ver el módulo de
codificación divergente del Skill 2 y la auditoría de color accesible del Skill 4.

Regla de decisión: usar Gi* para burbujas/clusters de valoración absoluta; usar
LISA (`modulo4b-lisa.md`) para clusters y anomalías de similitud local.

Pruebas múltiples (Wheeler): con n unidades, corregir `g.p_sim` por FDR/Bonferroni
antes de declarar hotspots (ver Skill 2,
`modulo3-mascara-significancia.md::correccion_multiple`); si no, ~5% serán
hotspots fantasma por azar.
