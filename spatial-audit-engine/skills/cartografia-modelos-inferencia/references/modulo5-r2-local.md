# Módulo 5 — Cartografía del R² local

```python
import mapclassify as mc

def mapa_r2_local(gdf, res, ax=None):
    """R² local: dónde el modelo explica bien vs. dónde colapsa."""
    gdf = gdf.copy()
    gdf["localR2"] = res.localR2.flatten()   # GWR/MGWR
    # rampa secuencial monocromática; intervalos precisos (no clases amplias)
    clf = mc.Quantiles(gdf["localR2"].values, k=6)
    ax = gdf.plot(column="localR2", scheme="quantiles", k=6, cmap="YlGn",
                  legend=True, edgecolor="0.85", linewidth=0.2, ax=ax)
    ax.set_axis_off()
    ax.set_title("R² local (poder explicativo del modelo)")
    return ax, clf
```

Reglas: paletas secuenciales monocromáticas (`YlGn`, `Purples`), intervalos
precisos para delimitar fronteras de rendimiento. Zonas de R² bajo señalan
submercados con otra lógica o variables omitidas (tenencia, informalidad). Este
mapa es OBLIGATORIO junto a toda superficie de coeficientes.
