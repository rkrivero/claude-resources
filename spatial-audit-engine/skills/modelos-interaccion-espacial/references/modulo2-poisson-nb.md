# Módulo 2 — Regresión de Poisson y Binomial Negativo para flujos OD

## Especificación completa

La forma estándar del modelo de interacción con Poisson:

```
E[T_ij] = exp(α + β₁·ln(O_i) + β₂·ln(D_j) + β₃·ln(d_ij) + δ_i + γ_j)
```

donde `δ_i` y `γ_j` son efectos fijos de origen y destino (capturan la
heterogeneidad no observada en cada nodo).

## Estimación

```python
import statsmodels.formula.api as smf
import statsmodels.api as sm
import numpy as np
import pandas as pd

def estimar_poisson(
    df: pd.DataFrame,
    col_tij: str,
    col_oi: str,
    col_dj: str,
    col_dist: str,
    col_origen: str,
    col_destino: str,
    controles_extra: list[str] | None = None
) -> sm.regression.linear_model.RegressionResultsWrapper:
    """
    Estima modelo de interacción espacial con Poisson.
    Efectos fijos de origen y destino incluidos.
    """
    extras = " + ".join(controles_extra) if controles_extra else ""
    formula = (
        f"{col_tij} ~ np.log({col_oi}) + np.log({col_dj}) + np.log({col_dist})"
        f" + C({col_origen}) + C({col_destino})"
        + (f" + {extras}" if extras else "")
    )
    modelo = smf.poisson(formula, data=df).fit(
        maxiter=300,
        method="bfgs",
        disp=False
    )
    print(modelo.summary2())
    return modelo


def estimar_negbin(
    df: pd.DataFrame,
    col_tij: str,
    col_oi: str,
    col_dj: str,
    col_dist: str,
    col_origen: str,
    col_destino: str
) -> sm.regression.linear_model.RegressionResultsWrapper:
    """Estima modelo Binomial Negativo (maneja sobredispersión)."""
    formula = (
        f"{col_tij} ~ np.log({col_oi}) + np.log({col_dj}) + np.log({col_dist})"
        f" + C({col_origen}) + C({col_destino})"
    )
    modelo = smf.negativebinomial(formula, data=df).fit(
        maxiter=300,
        method="bfgs",
        disp=False
    )
    print(modelo.summary2())
    return modelo
```

## Test de sobredispersión

```python
def test_sobredispersion(
    df: pd.DataFrame,
    col_tij: str,
    modelo_poisson
) -> dict:
    """
    Test de Cameron & Trivedi (1990).
    H0: equidispersión (Poisson es adecuado).
    H1: sobredispersión → usar Binomial Negativo.
    Regresión auxiliar: (y − μ̂)² / μ̂ ~ μ̂
    """
    mu = modelo_poisson.predict()
    y  = df[col_tij].values
    aux_y = (y - mu)**2 / mu
    aux_X = sm.add_constant(mu)
    aux_model = sm.OLS(aux_y, aux_X).fit()

    coef = aux_model.params[1]
    p    = aux_model.pvalues[1]
    if p < 0.05:
        print(f"Sobredispersión confirmada (coef={coef:.4f}, p={p:.4f}). "
              "Usar Binomial Negativo.")
    else:
        print(f"Sin evidencia de sobredispersión (coef={coef:.4f}, p={p:.4f}). "
              "Poisson es adecuado.")
    return {"coef": coef, "p": p, "sobredispersion": p < 0.05}
```

## Comparación de modelos

```python
def comparar_modelos(modelos: dict) -> pd.DataFrame:
    """
    modelos: {'Poisson': m1, 'NegBin': m2, ...}
    Devuelve tabla con AIC, BIC, log-likelihood.
    """
    filas = []
    for nombre, m in modelos.items():
        filas.append({
            "Modelo": nombre,
            "AIC": round(m.aic, 2),
            "BIC": round(m.bic, 2),
            "Log-L": round(m.llf, 2),
            "N": int(m.nobs)
        })
    tabla = pd.DataFrame(filas).sort_values("AIC")
    print(tabla.to_string(index=False))
    return tabla
```

## Interpretación de coeficientes

En modelos Poisson/NB, los coeficientes son log-multiplicativos:
- `exp(β)` = factor de cambio multiplicativo en el flujo esperado.
- Para `ln(d_ij)`: `exp(β_dist)` = elasticidad de distancia.

```python
def elasticidades(modelo, variables: list[str]) -> pd.DataFrame:
    """Extrae elasticidades (exp(β)) para variables en escala log."""
    params = modelo.params
    conf = modelo.conf_int(alpha=0.05)
    filas = []
    for v in variables:
        # Buscar el parámetro que coincida (puede ser 'np.log(var)')
        matches = [p for p in params.index if v in p and "np.log" in p]
        if not matches:
            continue
        p = matches[0]
        filas.append({
            "Variable": v,
            "β": round(params[p], 4),
            "exp(β)": round(np.exp(params[p]), 4),
            "IC 95% inf": round(np.exp(conf.loc[p, 0]), 4),
            "IC 95% sup": round(np.exp(conf.loc[p, 1]), 4),
        })
    return pd.DataFrame(filas)
```

## Manejo de ceros estructurales

Si la matriz OD tiene pares con flujo cero porque la interacción es IMPOSIBLE
(e.g., origen y destino son la misma unidad, o barreras físicas infranqueables):

```python
def filtrar_ceros_estructurales(
    df: pd.DataFrame,
    col_tij: str,
    col_origen: str,
    col_destino: str
) -> pd.DataFrame:
    """Excluye pares origen = destino y pares con flujo T_ij = 0 por diseño."""
    df = df[df[col_origen] != df[col_destino]].copy()
    n_ceros = (df[col_tij] == 0).sum()
    n_total = len(df)
    ratio = n_ceros / n_total
    if ratio > 0.5:
        print(f"ADVERTENCIA: {ratio*100:.1f}% de flujos son 0. "
              "Considerar Zero-Inflated Poisson o modelo hurdle.")
    return df
```

## Reporte APA 7

```
Se estimó un modelo de interacción espacial con regresión de Poisson,
incluyendo efectos fijos de origen y destino para controlar la heterogeneidad
no observada de los nodos (AIC = 4823.6). La prueba de sobredispersión de
Cameron y Trivedi (1990) no rechazó la equidispersión (coef = 0.12, p = .21),
confirmando la adecuación del modelo de Poisson. La elasticidad de distancia
fue β = −1.84 (exp(β) = 0.16, IC 95% [0.11, 0.23]), indicando que un
incremento del 10% en la distancia reduce el flujo esperado en ~16%.
```
