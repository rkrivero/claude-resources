# Módulo 3 — Las 17 categorías de hot/cold spot emergente

## Definición formal

Cada unidad espacial se clasifica en función de:
1. `frec_pos`: proporción de períodos en que Gi* fue significativamente positivo.
2. `frec_neg`: proporción de períodos en que Gi* fue significativamente negativo.
3. `τ` (tau de Mann-Kendall): pendiente de la tendencia de Gi* a lo largo del tiempo.
4. `mk_sig`: si la tendencia Mann-Kendall es estadísticamente significativa.
5. `consecutivo`: si los períodos significativos son consecutivos (no intercalados).
6. `reciente`: si la significancia ocurre predominantemente en los últimos períodos.

## Las 17 categorías

### Hot spots (Gi* positivo significativo)

| Nº | Categoría | Criterio simplificado |
|----|-----------|----------------------|
| 1  | **Persistent Hot Spot** | frec_pos ≥ 90% de los períodos |
| 2  | **Consecutive Hot Spot** | Significativo en todos los períodos recientes, sin interrupción; tendencia ↑ |
| 3  | **Intensifying Hot Spot** | frec_pos < 90%, τ significativamente positivo (se intensifica) |
| 4  | **New Hot Spot** | No era significativo antes; solo significativo en el último período |
| 5  | **Emerging Hot Spot** | Significativo en los períodos más recientes; τ positivo y significativo |
| 6  | **Sporadic Hot Spot** | Significativo en < 50% de períodos, no consecutivos, sin tendencia |
| 7  | **Oscillating Hot Spot** | Alterna: significativo → no sig → significativo en patrón regular |
| 8  | **Historical Hot Spot** | Significativo en el pasado pero ya NO en los períodos recientes |
| 9  | **Diminishing Hot Spot** | Frecuente pero τ significativamente negativo (se debilita) |

### Cold spots (Gi* negativo significativo)

| Nº | Categoría | Criterio simplificado |
|----|-----------|----------------------|
| 10 | **Persistent Cold Spot** | frec_neg ≥ 90% de los períodos |
| 11 | **Consecutive Cold Spot** | Sig. negativo en todos los períodos recientes, sin interrupción |
| 12 | **Intensifying Cold Spot** | frec_neg < 90%, τ significativamente negativo |
| 13 | **New Cold Spot** | No era cold spot antes; solo en el último período |
| 14 | **Emerging Cold Spot** | Sig. negativo en períodos recientes; τ negativo y significativo |
| 15 | **Sporadic Cold Spot** | < 50% períodos, no consecutivo, sin tendencia |
| 16 | **Oscillating Cold Spot** | Alterna entre cold spot y sin patrón |
| 17 | **Diminishing Cold Spot** | Frecuente como cold spot pero τ positivo (se debilita) |

### Sin patrón

| Nº | Categoría | Criterio |
|----|-----------|----------|
| 0  | **No Pattern** | No alcanza ninguna de las condiciones anteriores |

## Implementación de referencia

```python
import numpy as np
import pandas as pd

def clasificar_17_full(
    gi_pos: pd.DataFrame,    # bool: unidad × período (Gi* sig > 0)
    gi_neg: pd.DataFrame,    # bool: unidad × período (Gi* sig < 0)
    mk: pd.DataFrame,        # columnas: tau, p, significativo
    umbral_persistente: float = 0.90,
    umbral_historico: float = 0.50,
    n_recientes: int = 3,
    alpha: float = 0.05
) -> pd.Series:
    """
    Clasificación completa de 17 categorías.
    n_recientes: cuántos períodos finales se consideran "recientes".
    """
    n_periodos = gi_pos.shape[1]
    ultimos = gi_pos.columns[-n_recientes:]

    frec_pos = gi_pos.mean(axis=1)
    frec_neg = gi_neg.mean(axis=1)
    tau = mk["tau"]
    sig_mk = mk["p"] < alpha

    # Recientes significativos
    reciente_pos = gi_pos[ultimos].all(axis=1)
    reciente_neg = gi_neg[ultimos].all(axis=1)

    # Solo último período
    ultimo = gi_pos.columns[-1]
    nuevo_pos = gi_pos[ultimo] & (gi_pos.iloc[:, :-1].sum(axis=1) == 0)
    nuevo_neg = gi_neg[ultimo] & (gi_neg.iloc[:, :-1].sum(axis=1) == 0)

    # Histórico: sig en pasado pero no reciente
    pasado_pos = (gi_pos.iloc[:, :-n_recientes].mean(axis=1) > umbral_historico)
    hist_pos = pasado_pos & ~gi_pos[ultimos].any(axis=1)
    pasado_neg = (gi_neg.iloc[:, :-n_recientes].mean(axis=1) > umbral_historico)
    hist_neg = pasado_neg & ~gi_neg[ultimos].any(axis=1)

    categorias = []
    for idx in gi_pos.index:
        fp = frec_pos[idx]
        fn = frec_neg[idx]
        t  = tau[idx]
        sm = sig_mk[idx]

        if fp >= umbral_persistente:
            cat = "Persistent Hot Spot"
        elif nuevo_pos[idx]:
            cat = "New Hot Spot"
        elif reciente_pos[idx] and sm and t > 0:
            cat = "Emerging Hot Spot"
        elif fp > 0 and sm and t > 0:
            cat = "Intensifying Hot Spot"
        elif fp > 0 and sm and t < 0:
            cat = "Diminishing Hot Spot"
        elif hist_pos[idx]:
            cat = "Historical Hot Spot"
        elif fp > 0 and sm and abs(t) < 0.1:
            cat = "Consecutive Hot Spot"
        elif fp > 0:
            cat = "Sporadic Hot Spot"
        elif fn >= umbral_persistente:
            cat = "Persistent Cold Spot"
        elif nuevo_neg[idx]:
            cat = "New Cold Spot"
        elif reciente_neg[idx] and sm and t < 0:
            cat = "Emerging Cold Spot"
        elif fn > 0 and sm and t < 0:
            cat = "Intensifying Cold Spot"
        elif fn > 0 and sm and t > 0:
            cat = "Diminishing Cold Spot"
        elif hist_neg[idx]:
            cat = "Historical Cold Spot"
        elif fn > 0:
            cat = "Sporadic Cold Spot"
        else:
            cat = "No Pattern"

        categorias.append(cat)

    return pd.Series(categorias, index=gi_pos.index, name="categoria_hotspot")
```

## Paleta de colores recomendada

```python
PALETA_17 = {
    "Persistent Hot Spot":    "#b30000",
    "Consecutive Hot Spot":   "#e34a33",
    "Intensifying Hot Spot":  "#fc8d59",
    "Emerging Hot Spot":      "#fdcc8a",
    "New Hot Spot":           "#fef0d9",
    "Sporadic Hot Spot":      "#fdd49e",
    "Oscillating Hot Spot":   "#d9a026",
    "Historical Hot Spot":    "#bcbddc",
    "Diminishing Hot Spot":   "#9ecae1",
    "Persistent Cold Spot":   "#023858",
    "Consecutive Cold Spot":  "#0570b0",
    "Intensifying Cold Spot": "#3690c0",
    "Emerging Cold Spot":     "#74a9cf",
    "New Cold Spot":          "#d0d1e6",
    "Sporadic Cold Spot":     "#a6bddb",
    "Oscillating Cold Spot":  "#5e9bb8",
    "Historical Cold Spot":   "#d9d9d9",
    "Diminishing Cold Spot":  "#c0d4df",
    "No Pattern":             "#f0f0f0",
}
```

## Reglas de reporte

- Reportar la distribución de categorías como tabla (n y % por categoría).
- Mapear usando la paleta estandarizada con leyenda completa.
- Para tesis: agrupar en 4 macro-categorías si las 17 resultan en categorías vacías
  (Hot spot persistente, Hot spot emergente, Cold spot, Sin patrón).
- Incluir el período cubierto y la granularidad temporal en la nota de la figura.
