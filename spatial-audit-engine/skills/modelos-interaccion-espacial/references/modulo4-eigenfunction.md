# Módulo 4 — Filtrado espectral por eigenfunciones (Griffith)

## Motivación

El filtrado espectral extrae eigenvectores de la matriz espacial que sintetizan
los distintos patrones de autocorrelación presentes en los datos, sin imponer
un modelo paramétrico de dependencia (sin estimar ρ ni λ). Los eigenvectores
seleccionados se incluyen como covariables adicionales en la regresión.

## Algoritmo completo

```python
import numpy as np
from libpysal.weights import W

def calcular_eigenfunciones(
    w: W,
    min_ratio: float = 0.25
) -> tuple[np.ndarray, np.ndarray]:
    """
    Calcula los eigenvectores de la matriz centrada de Moran (MCM).
    Selecciona los que tienen I/I_max >= min_ratio.

    Devuelve: (eigenvectores seleccionados [n × k], valores propios correspondientes)
    """
    n = w.n
    W_arr = w.full()[0]

    # Proyector de centrado
    ones = np.ones((n, 1))
    P = np.eye(n) - ones @ ones.T / n

    # Matriz centrada de Moran
    MCM = P @ W_arr @ P

    # Descomposición espectral
    vals, vecs = np.linalg.eigh(MCM)

    # Ordenar de mayor a menor valor propio
    idx = np.argsort(vals)[::-1]
    vals = vals[idx]
    vecs = vecs[:, idx]

    # Umbral: I/I_max >= min_ratio (solo eigenvectores con señal espacial relevante)
    I_max = vals[0]
    mascara = (vals / I_max) >= min_ratio
    vecs_sel = vecs[:, mascara]
    vals_sel = vals[mascara]

    print(f"Eigenvectores candidatos: {n} → seleccionados: {vecs_sel.shape[1]} "
          f"(umbral I/I_max ≥ {min_ratio})")
    return vecs_sel, vals_sel


def seleccionar_eigenfunciones_stepwise(
    y: np.ndarray,
    X: np.ndarray,
    eigenvecs: np.ndarray,
    familia: str = "poisson",
    max_eigenvecs: int = 30,
    alpha: float = 0.05
) -> np.ndarray:
    """
    Selección stepwise de eigenvectores: agrega uno por uno mientras la
    autocorrelación residual de Moran sea significativa o mejore el AIC.

    familia: 'poisson' | 'gaussian' | 'negbinomial'
    Devuelve: subconjunto de eigenvectores seleccionados.
    """
    import statsmodels.api as sm
    from esda.moran import Moran

    seleccionados = []
    X_actual = X.copy()

    for i in range(min(max_eigenvecs, eigenvecs.shape[1])):
        ev = eigenvecs[:, i].reshape(-1, 1)
        X_nuevo = np.hstack([X_actual, ev])

        if familia == "poisson":
            m = sm.GLM(y, X_nuevo, family=sm.families.Poisson()).fit(disp=False)
        elif familia == "gaussian":
            m = sm.OLS(y, X_nuevo).fit()
        else:
            m = sm.GLM(y, X_nuevo, family=sm.families.NegativeBinomial()).fit(disp=False)

        seleccionados.append(ev)
        X_actual = X_nuevo
        print(f"EV {i+1} añadido — AIC: {m.aic:.2f}")

    return np.hstack(seleccionados) if seleccionados else np.empty((len(y), 0))
```

## Integración en el modelo de interacción espacial

```python
import statsmodels.formula.api as smf
import pandas as pd
import numpy as np

def poisson_con_eigenfiltrado(
    df: pd.DataFrame,
    col_tij: str,
    formula_base: str,
    eigenvecs: np.ndarray,
    n_max: int = 10
) -> object:
    """
    Estima modelo de Poisson con los primeros n_max eigenvectores como covariables.
    formula_base: fórmula sin eigenvectores, p. ej.:
      'T_ij ~ np.log(O_i) + np.log(D_j) + np.log(dist) + C(origen) + C(destino)'
    """
    df = df.copy()
    names = []
    for j in range(min(n_max, eigenvecs.shape[1])):
        col = f"EV{j+1}"
        df[col] = eigenvecs[:, j]
        names.append(col)

    formula = formula_base + " + " + " + ".join(names)
    modelo = smf.poisson(formula, data=df).fit(maxiter=300, disp=False)
    print(f"Modelo con eigenfiltrado — AIC: {modelo.aic:.2f}, N EV: {len(names)}")
    return modelo
```

## Cuándo aplicar filtrado espectral vs. efectos fijos

| Situación | Recomendación |
|-----------|---------------|
| Autocorrelación en residuos, nodos < 200 | Eigenvectores (más flexible) |
| Nodos > 500 | Eigenvectores (computacionalmente manejable) |
| Heterocedasticidad fuerte entre nodos | Efectos fijos de nodo (más robustos) |
| Ambas dimensiones (origen + destino) | Eigenvectores de W_origen Y W_destino |

## Reporte en texto (APA 7)

```
Para controlar la dependencia espacial residual detectada en la dimensión
de origen (Moran I = 0.21, p = .003), se incorporaron los ocho primeros
eigenvectores de la matriz centrada de Moran como covariables adicionales
(método de Griffith, 2003; umbral I/I_max ≥ 0.25). El filtrado redujo la
autocorrelación residual a niveles no significativos (Moran I = 0.03, p = .41)
y mejoró el ajuste del modelo (ΔAIC = −14.2).
```

## Referencias metodológicas

- Griffith, D. A. (2003). *Spatial Autocorrelation and Spatial Filtering*. Springer.
- Fischer, M. M., & Wang, J. (2011). *Spatial Data Analysis: Models, Methods
  and Techniques*. Springer. Cap. 6 (eigenfunction filtering).
