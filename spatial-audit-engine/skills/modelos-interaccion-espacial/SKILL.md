---
name: modelos-interaccion-espacial
description: >
  Use this skill when the user wants to model FLOWS between origins and
  destinations — triggers include "modelo de gravedad", "gravity model",
  "flujos origen-destino", "matriz OD", "flujos de migración", "flujos
  comerciales", "interacción espacial", "Poisson para flujos", "binomial
  negativo para conteos", "doubly constrained", "singly constrained",
  "modelo de interacción espacial", "spatial interaction model",
  "accesibilidad potencial", "eigenfunction filtering", "filtrado
  espectral espacial", "autocorrelación en flows", "dependencia espacial
  en flujos", "spillover de interacciones". Covers gravity models
  (unconstrained, singly/doubly constrained), Poisson and negative
  binomial regression for OD flows, OD matrix autocorrelation, and
  eigenfunction spatial filtering. Produces reproducible Python
  (statsmodels, spreg, libpysal, numpy).
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill — Modelos de interacción espacial

Motor de análisis de flujos entre pares de lugares. Modela la cantidad de
interacciones (viajes, migrantes, exportaciones, llamadas, créditos) entre
un origen `i` y un destino `j` como función de las masas de origen/destino
y de la fricción de la distancia.

## Principio operativo

Los datos de flujos violan supuestos de regresión OLS estándar:
(a) son conteos no negativos con sobredispersión; (b) tienen dependencia
espacial en la dimensión de origen Y de destino; (c) la matriz OD no es
i.i.d. No usar OLS para flujos. Usar Poisson (o Binomial Negativo) con
efectos fijos de origen/destino como mínimo.

## Orden de ejecución

```
[Matriz OD con atributos de origen, destino y distancia]
   → [Auditar distribución de flujos (ceros, sobredispersión)]
   → [Elegir especificación (gravity / Poisson / NB)]
   → [Diagnóstico de dependencia espacial en residuos]
   → [Aplicar corrección (efectos fijos / eigenfunction)]
   → [Interpretar efectos y elasticidades]
```

## Módulo 1 — Modelo de gravedad clásico

Forma multiplicativa:
```
T_ij = G · O_i^α · D_j^β · d_ij^γ
```
donde `T_ij` = flujo de `i` a `j`, `O_i` = masa de origen, `D_j` = masa de
destino, `d_ij` = impedancia (distancia, tiempo, costo), `G` = constante de
proporcionalidad.

**Variantes por restricción:**

| Tipo | Restricción | Cuándo |
|------|-------------|--------|
| No constrained | Ninguna | Exploración inicial |
| Origin-constrained (singly) | `ΣⱼT_ij = O_i` | Presupuesto de origen fijo (viajes) |
| Destination-constrained (singly) | `ΣᵢT_ij = D_j` | Capacidad de destino fija (empleos) |
| Doubly constrained | Ambas | Redistribución (matriz de viajes residencia-trabajo) |

**Estimación por OLS log-linearizado** (solo para exploración):
```python
import numpy as np
import statsmodels.api as sm

def gravity_ols(df, col_tij, col_oi, col_dj, col_dist):
    """
    Estimación log-lineal del modelo de gravedad (solo exploratoria).
    Limitación: ignora ceros y sobredispersión.
    """
    y = np.log(df[col_tij].replace(0, np.nan)).dropna()
    idx = y.index
    X = sm.add_constant(np.column_stack([
        np.log(df.loc[idx, col_oi]),
        np.log(df.loc[idx, col_dj]),
        np.log(df.loc[idx, col_dist])
    ]))
    model = sm.OLS(y, X).fit()
    print(model.summary())
    return model
```

PROHIBIR interpretar R² del modelo OLS log-lineal como bondad de ajuste real
para flujos: ignora los ceros y produce sesgo de Jensen (log(E[T]) ≠ E[log(T)]).

Código: `references/modulo1-gravedad.md`.

## Módulo 2 — Regresión de Poisson y Binomial Negativo para flujos

**Poisson** (`statsmodels.formula.api.poisson`):
- Correcto para conteos no negativos incluyendo ceros.
- Supuesto: media = varianza (equidispersión). Si la varianza >> media → sobredispersión.
- Test de sobredispersión: regresión auxiliar `(y − ŷ)² / ŷ ~ ŷ` con OLS;
  coeficiente significativo → sobredispersión presente.

**Binomial Negativo** (`statsmodels.formula.api.negativebinomial`):
- Extend Poisson con parámetro de sobredispersión `α`.
- Preferido cuando la prueba de sobredispersión rechaza Poisson.
- Comparar AIC: si AIC_NB < AIC_Poisson → NB preferido.

```python
import statsmodels.formula.api as smf
import pandas as pd

def estimar_poisson_nb(df: pd.DataFrame, formula: str) -> dict:
    """
    formula ejemplo: 'T_ij ~ log(O_i) + log(D_j) + log(dist) + C(origen) + C(destino)'
    Los efectos fijos C(origen) + C(destino) controlan la heterogeneidad no observada.
    """
    pois = smf.poisson(formula, data=df).fit(maxiter=200)
    nb   = smf.negativebinomial(formula, data=df).fit(maxiter=200)

    print(f"AIC Poisson:   {pois.aic:.1f}")
    print(f"AIC Neg.Bin.:  {nb.aic:.1f}")

    # Test de sobredispersión
    mu = pois.predict()
    residuos = (df[formula.split('~')[0].strip()] - mu) ** 2 / mu
    import statsmodels.api as sm
    test = sm.OLS(residuos, sm.add_constant(mu)).fit()
    print(f"Test sobredispersión: coef={test.params[1]:.4f}, p={test.pvalues[1]:.4f}")

    return {"poisson": pois, "negbin": nb}
```

**Efectos fijos de origen y destino** son obligatorios cuando la especificación
no incluye todas las variables de masa (capturan heterogeneidad no observada en
cada nodo de la red).

Código: `references/modulo2-poisson-nb.md`.

## Módulo 3 — Diagnóstico de dependencia espacial en flujos

Los residuos de un modelo OD pueden mostrar autocorrelación tanto en la dimensión
de origen como en la de destino (y en ambas simultáneamente).

**Test de Moran para residuos de flujos:**
Requiere construir una matriz de pesos W para la red OD. Dos enfoques:
- `W_origen`: unidades de origen vecinas comparten el mismo destino → comprueba dependencia en la dimensión de origen.
- `W_destino`: unidades de destino vecinas reciben flujos de los mismos orígenes.

```python
from libpysal.weights import W
from esda.moran import Moran
import numpy as np

def moran_residuos_od(residuos: np.ndarray, w: W, permutations: int = 999):
    """residuos: array de residuos del modelo OD, ordenados por unidad."""
    moran = Moran(residuos, w, permutations=permutations)
    print(f"Moran I (residuos OD) = {moran.I:.4f}, p = {moran.p_sim:.4f}")
    return moran
```

Código: `references/modulo3-dependencia-od.md`.

## Módulo 4 — Filtrado espacial por eigenfunciones

Cuando los residuos del modelo OD presentan autocorrelación, el filtrado
espectral de Griffith extrae los eigenvectores de la matriz espacial que
capturan la estructura de autocorrelación residual sin imponer un modelo
paramétrico de dependencia.

**Algoritmo:**
1. Descomponer la matriz centrada de Moran: `MCM = (I − 11'/n) · W · (I − 11'/n)`.
2. Calcular eigenvectores de `MCM`.
3. Seleccionar eigenvectores cuyo pseudo-R² incremental supera un umbral
   (convención: I/Imax > 0.25 como señal de relevancia espacial).
4. Incluir los eigenvectores seleccionados como covariables adicionales en la
   regresión de Poisson/NB.

```python
import numpy as np
from libpysal.weights import W

def eigenfunciones_espaciales(w: W, min_ratio: float = 0.25) -> np.ndarray:
    """
    Devuelve eigenvectores de MCM con I/Imax >= min_ratio.
    """
    n = w.n
    W_arr = w.full()[0]
    ones = np.ones((n, 1))
    P = np.eye(n) - ones @ ones.T / n
    MCM = P @ W_arr @ P
    vals, vecs = np.linalg.eigh(MCM)
    idx = np.argsort(vals)[::-1]
    vals, vecs = vals[idx], vecs[:, idx]
    I_max = vals[0]
    seleccionados = vecs[:, vals / I_max >= min_ratio]
    print(f"Eigenvectores seleccionados: {seleccionados.shape[1]} de {n}")
    return seleccionados
```

Código: `references/modulo4-eigenfunction.md`.

## Reglas transversales

- SIEMPRE incluir efectos fijos de origen y destino o algún control equivalente
  para la heterogeneidad no observada en los nodos.
- NUNCA estimar con OLS log-lineal y reportar como modelo definitivo: es solo
  para diagnóstico exploratorio.
- Con flujos que incluyen muchos ceros estructurales (no hay interacción posible
  entre pares distantes), considerar modelos Zero-Inflated Poisson o hurdle.
- Interpretar los coeficientes en Poisson como efectos multiplicativos sobre el
  flujo esperado: `exp(β)` = factor de cambio relativo.
- Elasticidad de distancia `γ`: si |γ| < 1, la fricción es baja (flujos poco
  sensibles a distancia); si |γ| > 2, la interacción es muy localizada.

## Entrega final

1. Tabla comparativa OLS-log / Poisson / NB con AIC y resultado del test de
   sobredispersión.
2. Coeficientes del modelo preferido con IC 95%.
3. Elasticidades de masa y distancia interpretadas sustantivamente.
4. Diagnóstico de autocorrelación en residuos (Moran en dimensión origen/destino).
5. Si se aplica filtrado espectral: número de eigenvectores incluidos y ganancia
   en AIC respecto al modelo base.
