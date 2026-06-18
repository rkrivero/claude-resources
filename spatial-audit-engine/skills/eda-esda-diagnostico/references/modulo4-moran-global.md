# Módulo 4 — Autocorrelación global y diagrama de Moran (esda)

```python
import numpy as np
import matplotlib.pyplot as plt
from esda.moran import Moran
from libpysal.weights import lag_spatial

def moran_global(y, w, permutations=999):
    w.transform = "r"
    mi = Moran(y, w, permutations=permutations)
    print(f"Moran's I = {mi.I:.4f}   p_sim = {mi.p_sim:.4f}   z = {mi.z_sim:.2f}")
    if mi.p_sim < 0.05:
        if mi.I > 0:
            print("Patrón: CONCENTRACIÓN/segregación (clusters HH y LL).")
        else:
            print("Patrón: DISPERSIÓN tipo tablero de ajedrez.")
    else:
        print("No se rechaza CSR (aleatoriedad espacial completa).")
    return mi

def diagrama_moran(y, w):
    """z vs. rezago espacial estandarizado. Pendiente = Moran global. Devuelve cuadrantes."""
    w.transform = "r"
    z = (y - y.mean()) / y.std()
    lag_z = lag_spatial(w, z)

    # cuadrantes: 1=HH, 2=LH, 3=LL, 4=HL
    cuad = np.zeros(len(z), dtype=int)
    cuad[(z > 0) & (lag_z > 0)] = 1  # HH
    cuad[(z < 0) & (lag_z > 0)] = 2  # LH
    cuad[(z < 0) & (lag_z < 0)] = 3  # LL
    cuad[(z > 0) & (lag_z < 0)] = 4  # HL

    fig, ax = plt.subplots()
    ax.scatter(z, lag_z, s=10, c="0.4")
    b = np.polyfit(z, lag_z, 1)[0]
    xs = np.linspace(z.min(), z.max(), 50)
    ax.plot(xs, b * xs, color="crimson")
    ax.axhline(0, color="k", lw=0.5); ax.axvline(0, color="k", lw=0.5)
    ax.set_xlabel("z (variable estandarizada)"); ax.set_ylabel("rezago espacial de z")
    ax.set_title(f"Diagrama de Moran (pendiente={b:.3f})")
    return cuad   # alimenta al Skill 2 (LISA / máscaras)
```

Los cuadrantes (HH/LL/HL/LH) son el insumo del análisis local. La inferencia de
Moran SIEMPRE por permutaciones (≥999), nunca solo analítica.
