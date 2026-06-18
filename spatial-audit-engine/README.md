# spatial-audit-engine

Motor de auditoría científica para análisis espacial urbano. Encadena siete skills que reproducen el flujo metodológico de Rey, Anselin, Brewer y la estadística espacial moderna: limpiar el terreno estadístico-geográfico antes de modelar, detectar patrones locales sin caer en falsos positivos, especificar el modelo econométrico correcto, comunicarlo con cartografía honesta, controlar sesgos de nivel revisor, asegurar reproducibilidad y sostener el plan de investigación.

Cada skill **genera código Python reproducible** (PySAL, GeoPandas, esda, libpysal, spreg, mgwr, mapclassify, seaborn) con las **reglas de decisión incrustadas**: no ofrece un menú de opciones, aplica criterios y justifica cada elección estadística.

## Componentes

| Skill | Qué hace |
|-------|----------|
| `eda-esda-diagnostico` | Auditoría de ingesta y geometría (CRS, densidad), perfilado EDA, matriz de pesos W, Moran global, LISA (Moran local) y clasificación óptima (Fisher-Jenks/ADCM). |
| `econometria-hedonica-espacial` | OLS base, árbol de tests LM de Anselin, estimación SLM/SEM/SAC/SDM, efectos directos/indirectos/totales y validación. Origen de los modelos y residuos. |
| `cartografia-modelos-inferencia` | Residuos OLS/SAR/SEM, superficies GWR/MGWR multiescala, máscara de significancia (|t|≥1.96), paletas divergentes ancladas en 0 y R² local. |
| `composicion-lienzo-cartografico` | Jerarquía visual (Brewer), control de colisiones de etiquetas (`adjustText`), halos/path effects, mapa base mudo (`contextily`) y export calibrado al medio (impreso vs. pantalla). |
| `entorno-pipeline-reproducible` | Mapeo al ecosistema PySAL, entorno `conda-forge`/`pixi`, arquitectura `src/` modular con handoffs en GeoPackage y contingencias (MGWR→ESF, `adjustText`→agregación/`plotly`/`folium`, dependency hell). Incluye `pixi.toml` y `environment.yml` de ejemplo. |
| `control-sesgos-validacion` | Compuertas de auditoría nivel revisor: VIF local (multicolinealidad en GWR/MGWR), validación cruzada espacial (Spatial Block CV / data leakage), falacia ecológica y consistencia de escala, endogeneidad/variable omitida, correlación espuria por topografía (detrending) y robustez multi-escala de los coeficientes (MAUP). |
| `propuesta-viabilidad-espacial` | Modo research proposal / plan de tesis: defensa del tamaño muestral (380 ideal para GWR/MGWR), las 4 figuras clave (cobertura dual KDE, EDA de asimetría, grafo de conectividad W, Moran scatterplot), checklist y guía de redacción de la sección metodológica. |

No incluye agentes, hooks ni servidores MCP: los siete skills operan sobre tus propios datos en el entorno local.

## Flujo recomendado

```
eda-esda-diagnostico ──► econometria-hedonica-espacial ──► cartografia-modelos-inferencia ──► composicion-lienzo-cartografico
   (audita datos, W,          (estima modelo global,            (residuos, GWR/MGWR,                (acabado y export
    Moran, LISA)               efectos, residuos)                máscaras, R² local)                 de la figura)
```

El primer skill entrega los artefactos que alimentan al resto: dataset reproyectado, matriz de pesos `W` auditada, diagnóstico de distribución + transformación recomendada, y los cuadrantes del diagrama de Moran / LISA. La estimación produce los modelos y residuos que la cartografía de inferencia mapea; la composición de lienzo da el acabado final a cualquier figura. El skill `entorno-pipeline-reproducible` es transversal: prepara el entorno, estructura el proyecto `src/` y resuelve los fallos comunes que aparecen al ejecutar todo lo anterior.

## Setup

No requiere variables de entorno. Las skills asumen un entorno Python con el stack geoespacial. Recomendado vía `pixi`:

```toml
# pixi.toml (dependencias clave)
[dependencies]
geopandas = "*"
libpysal = "*"
esda = "*"
spreg = "*"
mgwr = "*"
mapclassify = "*"
splot = "*"
pointpats = "*"
tobler = "*"
seaborn = "*"
matplotlib = "*"
contextily = "*"
geoplot = "*"
momepy = "*"
h3 = "*"
scipy = "*"
scikit-learn = "*"
statsmodels = "*"

[pypi-dependencies]
adjustText = "*"
colorspacious = "*"
```

## Uso

Invoca cada skill por su tema. Ejemplos de disparadores:

- "audita este shapefile / GeoJSON antes de modelar" → `eda-esda-diagnostico`
- "especifica el modelo hedónico espacial correcto" → `econometria-hedonica-espacial`
- "mapea residuos / coeficientes GWR-MGWR con máscara de significancia" → `cartografia-modelos-inferencia`
- "arma el layout del mapa y expórtalo para la tesis" → `composicion-lienzo-cartografico`
- "controla sesgos: VIF local, spatial CV, falacia ecológica" → `control-sesgos-validacion`
- "arma el entorno pixi / estructura el pipeline" → `entorno-pipeline-reproducible`
- "defiende la muestra y arma las figuras del plan de tesis" → `propuesta-viabilidad-espacial`

## Customización

Diseñado para el grupo de investigación cusco-estructura. Los ejemplos usan el contexto del Cusco metropolitano (UTM 19S, EPSG:32719), pero las reglas son agnósticas al caso de estudio: cambia el CRS objetivo y las variables y el flujo se mantiene.
