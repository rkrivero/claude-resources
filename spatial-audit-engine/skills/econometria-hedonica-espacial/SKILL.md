---
name: econometria-hedonica-espacial
description: >
  Use this skill when the user wants to estimate, specify, or select a spatial
  hedonic price model — triggers include "modelo hedónico", "estima el OLS",
  "tests LM de Anselin", "lag vs error", "SLM/SEM/SAC/SDM", "qué modelo espacial
  uso", "efectos directos e indirectos", "spillovers", "Common Factor", "valida
  el modelo espacial". Runs the OLS baseline, the Anselin LM/robust-LM decision
  tree, estimates SLM/SEM/SAC/SDM with spreg, decomposes direct/indirect/total
  effects, and validates. Inherits the transformed dependent variable and W from
  eda-esda-diagnostico; feeds estimated models and residuals to
  cartografia-modelos-inferencia. Produces reproducible Python (spreg).
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
---

# Skill 4 — Econometría hedónica espacial

Motor de especificación y estimación. Decide cuál modelo espacial corresponde a
los datos siguiendo el árbol de decisión de Anselin, lo estima con `spreg`,
descompone los efectos y valida. Genera código Python reproducible con el
criterio incrustado. Es el origen de los modelos que el skill
`cartografia-modelos-inferencia` cartografía.

## Principio operativo

No estimar un modelo espacial "porque sí". Primero correr OLS, leer sus
diagnósticos LM y dejar que la evidencia decida la especificación. Heredar la
transformación de la dependiente (log/Box-Cox) y la matriz `W` del skill
`eda-esda-diagnostico`; nunca recalcular `W` con criterios distintos a los ya
auditados.

## Insumos

- Variable dependiente transformada (típicamente `log(precio)`) y lambda de
  Box-Cox → de `eda-esda-diagnostico`.
- Matriz `W` estandarizada por filas, sin islas → de `eda-esda-diagnostico`.

## Módulo 1 — Línea base OLS y diagnóstico (spreg.OLS)

Estimar OLS con `spat_diag=True, moran=True`. Leer:
- Multicolinealidad (condition number), normalidad (Jarque-Bera),
  heterocedasticidad (Breusch-Pagan, Koenker-Bassett, White).
- Moran's I de los residuos: si es significativo, hay estructura espacial que el
  OLS ignora ⇒ avanzar al árbol de decisión.

Código: `references/modulo1-ols-base.md`.

## Módulo 1b — SLX como punto de partida (Vega & Elhorst)

Antes de saltar a SAR/SEM (máxima verosimilitud, spillovers globales poco
realistas para el suelo), probar el modelo SLX: añadir al OLS los rezagos
espaciales de las X (`WX`, promedio ponderado de las vecinas vía `libpysal`). El
precio de una manzana depende del entorno INMEDIATO (accesibilidad y topografía de
sus vecinas), no del comportamiento de toda la ciudad. SLX captura spillovers
locales puros, se estima directo por OLS y evita la endogeneidad de los modelos
globales rezagados. Usarlo como referencia antes de complicar la inferencia.

Código: `references/modulo7-slx.md`.

## Módulo 2 — Árbol de decisión LM de Anselin (lag vs. error)

Regla de decisión clásica (Anselin & Rey):
1. Mirar `LM-Lag` y `LM-Error`. Si ninguno es significativo ⇒ quedarse con OLS.
2. Si solo uno es significativo ⇒ elegir ese (SLM si lag, SEM si error).
3. Si ambos son significativos ⇒ comparar los robustos `RLM-Lag` y `RLM-Error`;
   elegir el robusto significativo. Si ambos robustos son significativos ⇒
   considerar SAC/SARAR o subir a SDM (posible variable omitida espacial).

Código y tabla de decisión: `references/modulo2-arbol-lm.md`.

## Módulo 3 — Estimación de modelos espaciales (spreg)

Estimar según la decisión del Módulo 2:
- **SLM** (rezago): `spreg.ML_Lag` (o `GM_Lag` con instrumentos si hay
  endogeneidad/heterocedasticidad).
- **SEM** (error): `spreg.ML_Error` (o `GM_Error_Het` robusto a
  heterocedasticidad).
- **SAC/SARAR**: `spreg.GM_Combo_Het`.
- **SDM** (Durbin): incluir rezagos de las X (`WX`) además de `Wy`; útil cuando
  hay sospecha de variable espacial omitida. El test de Common Factor decide si
  SDM se reduce a SEM.

Reportar pseudo-R², log-likelihood, AIC y los diagnósticos espaciales del modelo
ajustado (Moran de residuos del modelo, idealmente no significativo).

Código: `references/modulo3-modelos-espaciales.md`.

## Módulo 4 — Efectos directos, indirectos y totales

En modelos con `Wy` (SLM, SDM, SAC) el coeficiente NO es el efecto marginal: hay
retroalimentación espacial. Descomponer en efectos directo, indirecto
(spillover) y total mediante la matriz `(I - ρW)^-1`. Reportar con inferencia por
simulación. En SEM puro no hay spillovers (el efecto = coeficiente).

Código: `references/modulo4-efectos.md`.

## Módulo 5 — Validación y selección final

- Comparar modelos por AIC/log-likelihood y por la desaparición de
  autocorrelación en residuos (Moran del modelo no significativo).
- Verificar estabilidad de coeficientes y signos coherentes con la teoría
  hedónica (p. ej. distancia al centro con signo negativo sobre el precio).
- Si la heterogeneidad espacial persiste (los residuos siguen agrupados o el
  ajuste varía por zona), NO forzar un modelo global: derivar al skill
  `cartografia-modelos-inferencia` para GWR/MGWR local.
- Si GWR/MGWR es computacionalmente inviable (miles de observaciones), usar
  Filtros Espaciales por Autovectores (ESF) como alternativa — ver el skill
  `entorno-pipeline-reproducible` (Módulo 4, Escenario A).

Código: `references/modulo5-validacion.md`.

## Módulo 6 — Regímenes espaciales (Anselin)

Cuando el territorio está fracturado por barreras físicas o socioeconómicas
drásticas, el efecto de un determinante no varía suave (GWR) sino a SALTOS
discretos (centro histórico consolidado vs. laderas informales). Antes de mapear
coeficientes continuos, contrastar con regímenes espaciales:

- **Técnica:** estimar `spreg.OLS_Regimes` / `ML_Lag_Regimes` con una variable de
  régimen (zona) y leer el test de Chow (global y por variable). Chow significativo
  ⇒ la muestra debe dividirse en subregiones independientes.
- **Visual:** delimitar las fronteras de mercado con líneas estructurales gruesas
  y representar los coeficientes como BLOQUES por régimen, no como superficie
  suavizada. Acompañar con `seaborn` facetado por régimen (`lmplot`/`relplot`) para
  ver cómo cambia la pendiente al cruzar la frontera urbana.

Código: `references/modulo6-regimenes-espaciales.md`.

## Entrega final

Exportar: el modelo seleccionado con su justificación (qué dijo el árbol LM), el
vector de residuos para diagnóstico, y la tabla de efectos
directos/indirectos/totales. Estos artefactos alimentan al skill
`cartografia-modelos-inferencia`.
