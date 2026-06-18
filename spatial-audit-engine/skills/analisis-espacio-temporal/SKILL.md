---
name: analisis-espacio-temporal
description: >
  Use this skill when the user wants to detect patterns that combine SPACE and
  TIME simultaneously — triggers include "análisis espacio-temporal", "cubo
  espacio-temporal", "space-time cube", "hot spots emergentes", "patrones
  temporales de clustering", "tendencia de hot spots", "cómo evolucionan los
  clusters en el tiempo", "STAC", "STKO", "MTUP", "emerging hot spot",
  "persistent hot spot", "oscilating hot spot", "intensifying cold spot",
  "cuándo apareció el cluster", "ha crecido el cluster", "análisis Mann-Kendall
  espacial", "corrido de hot spots en el tiempo", "spatiotemporal LISA".
  Covers space-time cube construction (MTUP warnings), the 17 emerging hot
  spot pattern categories, spatiotemporal LISA, and visualization of temporal
  trajectories in geographic space. Produces reproducible Python.
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill — Análisis espacio-temporal

Motor de análisis de patrones que evolucionan en el espacio Y en el tiempo.
Detecta cuándo y dónde aparecen, crecen, declinan u oscilan los clusters espaciales.
Requiere dataset con coordenadas geográficas y al menos 10 períodos temporales
para que el análisis de tendencias sea robusto.

## Principio operativo

El espacio y el tiempo no son independientes. Un hot spot de hoy puede ser el
legado de un proceso de décadas (persistente) o un fenómeno reciente (emergente).
Distinguir entre ambos cambia la intervención política y la interpretación causal.
NUNCA reportar hot spots estáticos (Gi*) como equivalentes a hot spots temporales.

## Orden de ejecución

```
[Dataset con t períodos × n unidades × variable]
   → [Construir cubo espacio-temporal]
   → [Detectar tendencias por celda (Mann-Kendall)]
   → [Clasificar las 17 categorías de hot spot]
   → [Validar contra MTUP]
   → [Mapear trayectorias temporales]
```

## Módulo 1 — Cubo espacio-temporal

El cubo organiza los datos en una estructura tridimensional:
`(unidad_espacial × período_temporal × variable)`.

```python
import pandas as pd
import geopandas as gpd
import numpy as np

def construir_cubo(gdf_series: list[gpd.GeoDataFrame],
                   col_valor: str,
                   col_id: str,
                   periodos: list) -> pd.DataFrame:
    """
    gdf_series: lista de GeoDataFrames, uno por período, mismo id de unidad.
    Devuelve DataFrame largo: id_unidad × período × valor.
    """
    frames = []
    for t, (gdf, periodo) in enumerate(zip(gdf_series, periodos)):
        tmp = gdf[[col_id, col_valor, "geometry"]].copy()
        tmp["periodo"] = periodo
        tmp["t"] = t
        frames.append(tmp)
    cubo = pd.concat(frames, ignore_index=True)
    return cubo

def pivot_cubo(cubo: pd.DataFrame, col_id: str, col_valor: str) -> pd.DataFrame:
    """Transforma el cubo largo en matriz (n_unidades × n_periodos)."""
    return cubo.pivot(index=col_id, columns="periodo", values=col_valor)
```

**MTUP (Modifiable Temporal Unit Problem).** Análogo al MAUP pero para el tiempo:
el resultado depende de cómo se define el período. Siempre explorar al menos
3 granularidades temporales (anual, trimestral, mensual) y reportar si los patrones
son estables entre ellas. Si cambian drásticamente con la granularidad, reportar
como INESTABILIDAD TEMPORAL y no sobre-interpretar.

Código: `references/modulo1-space-time-cube.md`.

## Módulo 2 — Tendencias locales (Mann-Kendall espacial)

Para cada celda del cubo (unidad espacial), calcular el test de Mann-Kendall sobre
la serie temporal de la variable: detecta si hay tendencia monótona creciente o
decreciente estadísticamente significativa.

```python
from scipy.stats import kendalltau
import numpy as np

def mann_kendall_celda(serie: np.ndarray) -> dict:
    """
    serie: array de valores ordenados cronológicamente para una unidad.
    Devuelve: tau, p-valor, sentido de la tendencia.
    """
    t_arr = np.arange(len(serie))
    tau, p = kendalltau(t_arr, serie)
    return {"tau": tau, "p": p, "tendencia": "creciente" if tau > 0 else "decreciente"}

def mk_espacial(cubo_pivot: pd.DataFrame, alpha: float = 0.05) -> pd.DataFrame:
    """Aplica Mann-Kendall a cada fila (unidad) del cubo pivot."""
    resultados = cubo_pivot.apply(
        lambda row: pd.Series(mann_kendall_celda(row.values)), axis=1
    )
    resultados["significativo"] = resultados["p"] < alpha
    return resultados
```

Código: `references/modulo2-mann-kendall-espacial.md`.

## Módulo 3 — Las 17 categorías de hot spot emergente

Combinación de Gi* local (en cada período) + tendencia Mann-Kendall +
presencia/ausencia histórica. Las 17 categorías se dividen en:

**Hot spots (Gi* positivo significativo):**
| Categoría | Descripción |
|-----------|-------------|
| Persistent | Gi* significativo en ≥ 90% de períodos |
| Consecutive | Gi* en períodos consecutivos; tendencia creciente |
| Intensifying | Frecuencia < 90%, pero τ de M-K creciente y significativo |
| New | Sin historia previa; Gi* significativo solo en períodos recientes |
| Emerging | Gi* en los últimos períodos; tendencia creciente significativa |
| Sporadic | Gi* en < 50% de períodos, no consecutivo, sin tendencia |
| Oscillating | Alterna significativo/no-significativo en patrones regulares |
| Historical | Gi* significativo en períodos pasados pero no recientes |
| Diminishing | Gi* frecuente pero τ de M-K decreciente significativo |

**Cold spots (Gi* negativo significativo):** las mismas 9 categorías con
prefijo "Cold": Persistent Cold, Consecutive Cold, Intensifying Cold, New Cold,
Emerging Cold, Sporadic Cold, Oscillating Cold, Historical Cold, Diminishing Cold.

**Sin patrón (0 o insuficiente):** No Pattern.

```python
def clasificar_17_categorias(
    gi_star_mask: pd.DataFrame,  # bool: unidad × período (Gi* sig > 0)
    gi_star_mask_neg: pd.DataFrame,  # bool: unidad × período (Gi* sig < 0)
    mk_results: pd.DataFrame,
    umbral_persistente: float = 0.9
) -> pd.Series:
    """
    Clasificación simplificada de las 17 categorías.
    Devuelve Serie con la categoría por unidad.
    """
    n_periodos = gi_star_mask.shape[1]
    frec_pos = gi_star_mask.mean(axis=1)
    frec_neg = gi_star_mask_neg.mean(axis=1)
    tau = mk_results["tau"]
    mk_sig = mk_results["significativo"]

    categorias = []
    for idx in gi_star_mask.index:
        fp = frec_pos[idx]
        fn = frec_neg[idx]
        t = tau[idx]
        s = mk_sig[idx]
        if fp >= umbral_persistente:
            cat = "Persistent Hot Spot"
        elif fp > 0 and s and t > 0:
            cat = "Intensifying Hot Spot" if fp < umbral_persistente else "Consecutive Hot Spot"
        elif fp > 0 and not s:
            cat = "Sporadic Hot Spot"
        elif fn >= umbral_persistente:
            cat = "Persistent Cold Spot"
        elif fn > 0 and s and t < 0:
            cat = "Intensifying Cold Spot"
        elif fn > 0 and not s:
            cat = "Sporadic Cold Spot"
        else:
            cat = "No Pattern"
        categorias.append(cat)
    return pd.Series(categorias, index=gi_star_mask.index)
```

Código completo y 17 categorías detalladas: `references/modulo3-17-categorias.md`.

## Módulo 4 — Visualización de trayectorias temporales

Para cada categoría, producir:
1. **Mapa de categorías**: coropleta con las 17 categorías codificadas por color.
   Hot spots: rojo → naranja. Cold spots: azul → cian. Sin patrón: gris.
2. **Serie temporal de Gi***: para las 5-10 unidades con Gi* más alto/bajo,
   graficar la trayectoria temporal (línea) del estadístico Gi* con banda de
   significancia (±1.96) como referencia.
3. **Mapa animado (opcional)**: GIF frame-by-frame de Gi* por período. Útil
   para presentaciones; NO para tesis (usar small multiples en su lugar).

Ver: skill `composicion-lienzo-cartografico` → `references/modulo8-small-multiples.md`
para la composición de paneles temporales.

## Reglas transversales

- Mínimo recomendado: **10 períodos** para que Mann-Kendall tenga potencia.
  Con < 6 períodos, reportar solo la clasificación estática (Gi* por período)
  sin el componente de tendencia.
- MTUP: siempre explorar más de una granularidad temporal y reportar sensibilidad.
- El análisis espacio-temporal es EXPLORATORIO. Para inferencia causal añadir
  panel data models o diferencias en diferencias espaciales.
- Bonferroni/FDR en Gi* por cada período antes de clasificar las categorías.

## Entrega final

1. Mapa de 17 categorías (o las categorías presentes en los datos).
2. Tabla resumen: n unidades por categoría, Gi* mediano, τ mediano.
3. Informe de sensibilidad MTUP (≥ 2 granularidades).
4. Series temporales de los clusters más relevantes.
