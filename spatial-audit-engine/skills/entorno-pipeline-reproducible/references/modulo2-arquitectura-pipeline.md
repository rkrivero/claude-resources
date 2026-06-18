# Módulo 2 — Arquitectura modular del pipeline

Tres módulos independientes que se pasan GeoPackages limpios. Nunca un script
monolítico.

```
proyecto/
├── pixi.toml
├── data/
│   ├── raw/            # crudo (scraping, catastro)
│   └── interim/        # .gpkg intermedios entre etapas
├── src/
│   ├── eda_esda.py            # Skill 1
│   ├── spatial_models.py      # Skills 4 y 2
│   └── cartography_output.py  # Skill 3
└── outputs/            # figuras finales (PDF/PNG)
```

## Handoff por GeoPackage

```python
# --- src/eda_esda.py (Skill 1) ---
import geopandas as gpd
from libpysal.weights import Queen
from esda.moran import Moran_Local

def run(path_in, path_out):
    gdf = gpd.read_file(path_in).to_crs("EPSG:32719")
    w = Queen.from_dataframe(gdf, use_index=True); w.transform = "r"
    lm = Moran_Local(gdf["ln_precio"].values, w, permutations=999)
    gdf["lisa_q"] = lm.q
    gdf["lisa_p"] = lm.p_sim
    gdf.to_file(path_out, driver="GPKG")   # entrega intermedia

# --- src/spatial_models.py (Skills 4 y 2) ---
def run(path_in, path_out):
    gdf = gpd.read_file(path_in)
    # ... estima con spreg/mgwr, aplica |t|>=1.96, añade columnas beta_/se_/localR2
    gdf.to_file(path_out, driver="GPKG")

# --- src/cartography_output.py (Skill 3) ---
def run(path_in, path_pdf):
    gdf = gpd.read_file(path_in)
    # ... compone lienzo, contextily mudo, halos, adjustText, export PDF 300 dpi
```

Orquestación simple (o vía `marimo`/`Quarto`):

```python
if __name__ == "__main__":
    import eda_esda, spatial_models, cartography_output
    eda_esda.run("data/raw/transacciones.gpkg", "data/interim/esda.gpkg")
    spatial_models.run("data/interim/esda.gpkg", "data/interim/modelos.gpkg")
    cartography_output.run("data/interim/modelos.gpkg", "outputs/mapa_final.pdf")
```

Cada módulo es testeable de forma aislada y reanudable desde su `.gpkg`.
