# Módulo 6 — Efectos de borde y mapa de error estándar local (Haining, Griffith)

Las unidades del perímetro tienen menos vecinos en `W` → falsa pérdida de
precisión en los márgenes. Caso agudo: **vecinos faltantes (missing neighbors)** —
la matriz `W` asume vacío absoluto más allá del límite administrativo e ignora
transacciones reales al otro lado de la calle si pertenecen a otra jurisdicción,
subestimando el rezago espacial y ensuciando los residuos del borde. Dos
mitigaciones.

## 1. Búfer de calibración (técnica)

```python
import geopandas as gpd
import pandas as pd

def buffer_calibracion(gdf_estudio, gdf_extra, limite_oficial, pct=0.10):
    """
    Calibra el modelo con datos ~pct más allá del límite; recorta el mapa al límite.
    gdf_extra: transacciones recolectadas fuera del límite oficial.
    """
    buf = limite_oficial.buffer(limite_oficial.length * pct / 4)  # margen aprox
    calib = gpd.overlay(gpd.GeoDataFrame(geometry=[buf], crs=gdf_estudio.crs),
                        gdf_extra, how="intersection")
    datos_calib = gpd.GeoDataFrame(
        pd.concat([gdf_estudio, calib], ignore_index=True), crs=gdf_estudio.crs)
    return datos_calib   # estimar con esto; mapear solo gdf_estudio
```

## 2. Mapa de error estándar local (visual)

```python
import matplotlib.pyplot as plt
import mapclassify as mc

def mapa_se_local(gdf, res, idx, nombre, ax=None):
    """Compañero OBLIGATORIO de cada superficie de coeficiente."""
    gdf = gdf.copy()
    gdf[f"se_{nombre}"] = res.bse[:, idx]      # error estándar local por unidad
    ax = gdf.plot(column=f"se_{nombre}", scheme="quantiles", k=5, cmap="OrRd",
                  legend=True, edgecolor="0.85", linewidth=0.2, ax=ax)
    ax.set_axis_off()
    ax.set_title(f"Error estándar local — {nombre} (más ancho en bordes = cautela)")
    return ax
```

Regla: presentar el par coeficiente + error estándar local. En los bordes el SE
crece; advertir explícitamente que las estimaciones marginales son menos fiables.
