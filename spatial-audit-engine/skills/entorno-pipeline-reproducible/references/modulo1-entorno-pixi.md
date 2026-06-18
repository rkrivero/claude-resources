# Módulo 1 — Entorno reproducible (pixi / conda-forge)

Regla de oro: un solo canal `conda-forge` para que GDAL/GEOS/PROJ y los wrappers
de Python queden binariamente alineados.

## Con pixi (recomendado)

```bash
# en la raíz del proyecto, junto a examples/pixi.toml
pixi install            # crea el entorno desde pixi.toml
pixi run python -c "import geopandas; print(geopandas.__version__)"
pixi add esda mgwr      # añadir paquetes conda-forge
pixi add --pypi adjustText colorspacious   # solo lo que no está en conda-forge
```

## Con conda/mamba

```bash
mamba env create -f examples/environment.yml
mamba activate cusco-estructura
```

Ver `../examples/pixi.toml` y `../examples/environment.yml` listos para copiar a
la raíz del proyecto de análisis (NO van dentro del plugin; son plantillas).

Reglas:
- Declarar TODO en el archivo de config; no instalar sueltos.
- `pip`/`--pypi` solo para paquetes ausentes en conda-forge, y dentro del entorno
  ya creado.
- Fijar versiones cuando un análisis deba ser exactamente reproducible
  (`pixi.lock` se versiona en git).
