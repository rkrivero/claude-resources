# Módulo 2 — Árbol de decisión LM de Anselin

Tabla de decisión (Anselin & Rey). `LM-Lag`, `LM-Error` y sus robustos `RLM-*`
salen del OLS con `spat_diag=True`.

| LM-Lag | LM-Error | Decisión |
|--------|----------|----------|
| no sig | no sig   | Quedarse con OLS |
| sig    | no sig   | SLM (rezago) |
| no sig | sig      | SEM (error) |
| sig    | sig      | Mirar robustos → el RLM significativo gana |
| ambos RLM sig | | SAC/SARAR o subir a SDM (posible variable omitida) |

```python
def arbol_lm(model, alpha=0.05):
    lm_lag = model.lm_lag[1]            # (stat, pvalue)
    lm_err = model.lm_error[1]
    rlm_lag = model.rlm_lag[1]
    rlm_err = model.rlm_error[1]
    sig = lambda p: p < alpha

    if not sig(lm_lag) and not sig(lm_err):
        return "OLS"
    if sig(lm_lag) and not sig(lm_err):
        return "SLM"
    if sig(lm_err) and not sig(lm_lag):
        return "SEM"
    # ambos LM significativos → robustos
    if sig(rlm_lag) and not sig(rlm_err):
        return "SLM"
    if sig(rlm_err) and not sig(rlm_lag):
        return "SEM"
    return "SAC/SDM"   # ambos robustos sig → combinado o Durbin
```
