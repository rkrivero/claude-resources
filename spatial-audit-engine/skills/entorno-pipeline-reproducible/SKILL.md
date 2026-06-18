---
name: entorno-pipeline-reproducible
description: >
  Use this skill when the user wants to set up the environment, scaffold the
  project, or troubleshoot the spatial analysis pipeline — triggers include
  "arma el entorno", "pixi / conda-forge", "environment.yml", "estructura el
  proyecto", "pipeline modular", "dependency hell", "segmentation fault al
  importar geopandas", "GDAL/GEOS/PROJ", "MGWR se cuelga / sin memoria", "ESF /
  eigenvector spatial filtering", "adjustText se congela", "migrar a plotly /
  folium". Maps the analysis to the PySAL ecosystem, builds a reproducible
  conda-forge/pixi environment, scaffolds the src/ modular pipeline with
  GeoPackage handoffs, and provides contingency plans for the common failures.
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill 5 — Entorno y pipeline reproducible

Capa de ingeniería que sostiene a los otros cuatro skills. Lleva la teoría a una
terminal que ejecuta: entorno geoespacial reproducible, arquitectura modular y
planes de contingencia cuando el software o la máquina fallan. Genera
configuración y código reproducible.

## Principio operativo

El ecosistema geoespacial de Python se apoya en librerías C++ (GDAL, GEOS, PROJ).
NUNCA mezclar binarios de fuentes distintas con `pip`. Aislar el entorno en un
solo canal (`conda-forge`) vía `pixi`/`conda`. Pasar datos entre etapas con
archivos limpios (GeoPackage), no con un script monolítico.

## Módulo 1 — Mapeo al ecosistema (PySAL)

Cada técnica tiene un repositorio mantenido, casi todos bajo la organización
PySAL:

| Técnica / autor | Paquete |
|-----------------|---------|
| Matriz W, contigüidad, KNN (Anselin, Rey) | `libpysal` |
| Moran global/local, Gi* (Anselin, Getis-Ord) | `esda` |
| GWR/MGWR, bandwidths (Fotheringham) | `mgwr` |
| Modelos globales, regímenes, ESF (Anselin) | `spreg` |
| Función K, patrones de puntos (Ripley) | `pointpats` |
| Clasificación sin sesgo (Jenks, ADCM) | `mapclassify` |
| Mapas LISA con colores estándar | `splot` |
| Interpolación areal / picnofiláctica (Tobler) | `tobler` |
| EDA, render, semiótica (Bertin, Brewer, Wilke) | `matplotlib`, `seaborn` |

## Módulo 2 — Entorno de dependencias (pixi / conda-forge)

Forzar un solo canal `conda-forge` garantiza que GDAL/GEOS/PROJ y sus wrappers
(`geopandas`, `shapely`) queden alineados. Ver `examples/pixi.toml` y
`examples/environment.yml` listos para copiar.

Reglas:
- Crear el entorno desde el archivo de config, no instalando sueltos.
- `pip` solo para lo que no está en conda-forge (p. ej. `adjustText`,
  `colorspacious`), y dentro del entorno ya creado.

Código y archivos: `references/modulo1-entorno-pixi.md`.

## Módulo 3 — Arquitectura del pipeline (src/ modular)

Tres módulos que se pasan GeoPackages limpios, espejo de los skills:

1. `src/eda_esda.py` (Skill 1) → carga, reproyecta, calcula `W` con `libpysal`,
   corre Moran local con `esda`, exporta `.gpkg` con geometrías + cuadrantes +
   p-valores.
2. `src/spatial_models.py` (Skills 4 y 2) → lee el `.gpkg`, estima global con
   `spreg` o local con `mgwr`, aplica el corte `|t|>=1.96` y añade las superficies
   analíticas al archivo.
3. `src/cartography_output.py` (Skill 3) → toma el dataset modelado, compone el
   lienzo, mapa base mudo, halos y `adjustText`, y exporta PDF de alta resolución.

Código y scaffolding: `references/modulo2-arquitectura-pipeline.md`.

## Módulo 4 — Planes de contingencia

**Escenario A — MGWR se cuelga / sin memoria.** El backfitting de MGWR es
O(N²)–O(N³); con miles de observaciones satura RAM o tarda días. NO volver a un
OLS global ciego. Alternativa: Filtros Espaciales por Autovectores (ESF,
`spreg`): extrae autovectores de `W` como variables proxy en una regresión lineal
normal, capturando heterogeneidad espacial sin bucles iterativos.

**Escenario B — `adjustText` congela el render.** Demasiadas etiquetas → bucle
infinito de repulsión. Alternativas: (1) etiquetar solo los ~10 valores más
atípicos (filtro con `mapclassify`), o (2) migrar la fase exploratoria a
`plotly`/`folium`, que delegan las colisiones al navegador con marker clusters y
zoom.

**Escenario C — dependency hell / segfault al importar GeoPandas.** Ruptura entre
binarios del SO y wrappers de Python. NO parchar con `pip`. Borrar el entorno y
reinstalar rígido desde `environment.yml`/`pixi.toml` con canal restringido a
`conda-forge`.

Código y diagnósticos: `references/modulo3-contingencias.md`.

## Entrega final

Un entorno reproducible declarado en archivo, un esqueleto `src/` de tres módulos
con handoffs en GeoPackage, y rutas de escape verificadas para los tres fallos
comunes. Confirmar el gestor (`pixi` vs. `conda`) antes de generar los archivos.
