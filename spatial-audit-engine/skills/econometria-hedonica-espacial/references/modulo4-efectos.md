# Módulo 4 — Efectos directos, indirectos y totales

En modelos con `Wy` (SLM, SDM, SAC) el coeficiente NO es el efecto marginal por
la retroalimentación espacial `(I - ρW)^-1`.

```python
import numpy as np

def efectos_slm(m, w, idx, k_sim=1000):
    """
    Descomposición LeSage-Pace para SLM:
      Total  = β / (1 - ρ)
      Direct ≈ promedio de la diagonal de (I-ρW)^-1 β
      Indirect (spillover) = Total - Direct
    """
    rho = m.rho
    beta = m.betas[idx + 1, 0]   # +1 por el intercepto
    total = beta / (1 - rho)
    n = w.n
    W = w.full()[0]
    M = np.linalg.inv(np.eye(n) - rho * W) * beta
    direct = np.trace(M) / n
    indirect = total - direct
    print(f"Directo={direct:.4f}  Indirecto={indirect:.4f}  Total={total:.4f}")
    return {"direct": direct, "indirect": indirect, "total": total}
```

En SEM puro no hay spillovers: el efecto = coeficiente. Reportar inferencia de los
efectos por simulación cuando sea posible. `spreg` también expone `m.sp_multipliers`
en algunas versiones para los multiplicadores espaciales.
