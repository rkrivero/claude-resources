# Módulo 1 — Modelo de gravedad: especificaciones y restricciones

## Forma general

```
T_ij = G · f(O_i) · g(D_j) · h(d_ij)
```

Forma log-lineal de estimación exploratoria:
```
ln(T_ij) = ln(G) + α·ln(O_i) + β·ln(D_j) + γ·ln(d_ij) + ε_ij
```

**Advertencia:** el OLS log-lineal produce estimadores sesgados (sesgo de Jensen:
`E[ln(T)] ≠ ln(E[T])`). Usarlo solo para diagnóstico inicial, nunca como modelo final.

## Variantes por restricción

### Sin restricción (unconstrained)

No garantiza que los totales de fila o columna coincidan con los observados.
Útil para predicción de nuevos flujos, no para redistribución de flujos existentes.

```python
import numpy as np
import statsmodels.api as sm

def gravedad_ols(ln_Tij, ln_Oi, ln_Dj, ln_dij):
    """Estimación log-lineal exploratoria (NO para inferencia final)."""
    X = sm.add_constant(np.column_stack([ln_Oi, ln_Dj, ln_dij]))
    modelo = sm.OLS(ln_Tij, X).fit()
    print(f"α={modelo.params[1]:.3f}, β={modelo.params[2]:.3f}, γ={modelo.params[3]:.3f}")
    print(f"R²={modelo.rsquared:.3f}")
    return modelo
```

### Origen restringido (singly constrained — origin)

```
T_ij = A_i · O_i · g(D_j) · h(d_ij)
```
donde `A_i = 1 / Σⱼ[g(D_j) · h(d_ij)]` garantiza `ΣⱼT_ij = O_i`.

Casos de uso: viajes donde el presupuesto de salida es conocido (p. ej., flujos
de egresados de una institución hacia el mercado laboral).

### Destino restringido (singly constrained — destination)

Análogo al anterior pero `ΣᵢT_ij = D_j` (capacidad del destino fija).

Casos de uso: distribución de demanda hacia oferta fija (puestos de trabajo,
camas hospitalarias).

### Doblemente restringido (doubly constrained)

```
T_ij = A_i · B_j · O_i · D_j · h(d_ij)
```
donde `A_i` y `B_j` son factores de balanceo calculados iterativamente (IPF:
Iterative Proportional Fitting).

```python
def ipf_gravity(O: np.ndarray, D: np.ndarray, impedancia: np.ndarray,
                max_iter: int = 200, tol: float = 1e-6) -> np.ndarray:
    """
    Doubly constrained gravity model via IPF.
    O: vector de totales de origen (n,)
    D: vector de totales de destino (m,)
    impedancia: matriz n × m de f(d_ij)
    Devuelve T: matriz n × m de flujos estimados.
    """
    n, m = len(O), len(D)
    A = np.ones(n)
    B = np.ones(m)
    T = np.outer(A * O, B * D) * impedancia

    for it in range(max_iter):
        # Balanceo por origen
        row_sum = T.sum(axis=1)
        A_new = O / (row_sum + 1e-12)
        T = (A_new[:, None] * O[:, None]) * (B[None, :] * D[None, :]) * impedancia
        # Balanceo por destino
        col_sum = T.sum(axis=0)
        B_new = D / (col_sum + 1e-12)
        T = (A_new[:, None] * O[:, None]) * (B_new[None, :] * D[None, :]) * impedancia

        delta = np.abs(A_new - A).max() + np.abs(B_new - B).max()
        A, B = A_new, B_new
        if delta < tol:
            print(f"IPF convergió en {it+1} iteraciones (delta={delta:.2e})")
            break
    else:
        print(f"ADVERTENCIA: IPF no convergió en {max_iter} iteraciones")
    return T
```

## Funciones de impedancia

| Función | Fórmula | Parámetro | Cuándo usar |
|---------|---------|-----------|-------------|
| Potencia | `d_ij^γ` | γ (exponente) | Flujos de largo alcance |
| Exponencial negativa | `exp(−β·d_ij)` | β (decaimiento) | Flujos de corto alcance |
| Normal log | `exp(−(ln(d))² / 2σ²)` | σ | Flujos con distancia óptima |
| Combinada | `d^α · exp(−β·d)` | α, β | Flujos con pico intermedio |

## Diagnóstico inicial de ajuste

```python
def diagnostico_gravedad(T_obs: np.ndarray, T_pred: np.ndarray) -> dict:
    """
    Métricas de ajuste entre flujos observados y predichos.
    """
    mask = T_obs > 0  # evitar log(0)
    ssi = 1 - np.sum((T_obs[mask] - T_pred[mask])**2) / np.sum((T_obs[mask] - T_obs[mask].mean())**2)
    rmse = np.sqrt(np.mean((T_obs - T_pred)**2))
    smape = 100 * np.mean(np.abs(T_obs - T_pred) / ((np.abs(T_obs) + np.abs(T_pred)) / 2 + 1e-12))
    print(f"SSI (Spatial Separation Index): {ssi:.4f}")
    print(f"RMSE: {rmse:.2f}")
    print(f"SMAPE: {smape:.2f}%")
    return {"ssi": ssi, "rmse": rmse, "smape": smape}
```
