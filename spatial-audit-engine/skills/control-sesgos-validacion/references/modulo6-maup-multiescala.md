# Compuerta 6 — Robustez multi-escala del coeficiente (MAUP, Openshaw)

El mismo determinante puede cambiar de magnitud y hasta de signo según la unidad
de agregación. Probar el hallazgo en varias escalas.

```python
import numpy as np
import pandas as pd
import geopandas as gpd
from spreg import OLS
from libpysal.weights import Queen

def beta_por_escala(gdf_puntos, y_col, x_cols, escalas_h3=(8, 9, 10)):
    """
    Corre el modelo en grillas H3 de distinta resolución y compara el coeficiente
    de cada variable. Requiere una función de agregación a H3 (ver Skill 1 modulo1b).
    """
    from importlib import import_module
    # reusar a_hexagonos del Skill 1 (copiada al proyecto) o equivalente
    filas = []
    for res in escalas_h3:
        agg = _agregar_h3(gdf_puntos, res, y_col, x_cols)   # define en el proyecto
        w = Queen.from_dataframe(agg, use_index=True); w.transform = "r"
        m = OLS(agg[[y_col]].values, agg[x_cols].values, w=w, name_x=x_cols)
        for j, xc in enumerate(x_cols):
            filas.append({"escala": f"H3-{res}", "n": agg.shape[0],
                          "variable": xc, "beta": m.betas[j+1, 0]})
    tabla = pd.DataFrame(filas).pivot(index="variable", columns="escala", values="beta")
    print(tabla)
    return tabla

def veredicto_robustez(tabla):
    """Estable en signo y magnitud => robusto; cambia de signo => artefacto MAUP."""
    for var, fila in tabla.iterrows():
        signos = np.sign(fila.values)
        estable = np.all(signos == signos[0])
        print(f"{var}: {'ROBUSTO' if estable else 'ARTEFACTO MAUP (cambia de signo)'}")
```

Regla: presentar la tabla de coeficientes por escala (manzana + H3 8/9/10) en la
tesis. Solo defender determinantes estables en todas las escalas. Implementar
`_agregar_h3` con la función `a_hexagonos` del skill `eda-esda-diagnostico`
(modulo1b).
