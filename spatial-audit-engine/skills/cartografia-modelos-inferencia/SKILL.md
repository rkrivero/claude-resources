---
name: cartografia-modelos-inferencia
description: >
  Use this skill when the user wants to diagnose, audit, or map the OUTPUT of
  spatial regression models — triggers include "mapea los residuos del OLS/SAR/
  SEM", "cartografía de error", "Moran de residuos", "superficies de
  coeficientes GWR/MGWR", "ancho de banda óptimo", "máscara de significancia
  local", "|t|>=1.96", "paleta divergente anclada en cero", "R2 local". Audits
  model residuals (spreg), maps multiscale local coefficients (mgwr), builds
  honest significance masks, applies divergent palettes anchored at zero, and
  maps local R². Consumes estimated models (see econometria-hedonica-espacial)
  and the W matrix from eda-esda-diagnostico. Produces reproducible Python.
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill 2 — Cartografía de modelos y máscaras de inferencia

Motor de inferencia honesta sobre la salida de modelos econométricos espaciales.
Prueba que un modelo global es inadecuado, lleva el análisis a lo local y
PROHÍBE pintar de color cualquier zona que no supere el umbral de significancia.
Genera código Python reproducible con el criterio incrustado.

## Principio operativo

No discutir resultados sobre zonas sin validez científica. Toda superficie de
coeficientes pasa por la máscara de significancia ANTES de colorearse. El cero
estadístico ancla toda paleta divergente. El R² local acompaña obligatoriamente
a cualquier mapa de coeficientes.

## Insumos y dependencias

- Modelos estimados (OLS, SAR/ML_Lag, SEM/ML_Error) → del skill
  `econometria-hedonica-espacial`.
- Matriz `W` auditada y estandarizada → del skill `eda-esda-diagnostico`.
- Para el render final (composición de lienzo, halos, export) → delega en el
  skill `composicion-lienzo-cartografico`.

## Módulo 1 — Auditoría de residuos y no-estacionariedad (spreg)

**Obtiene:** vectores de residuos crudos `e = y - ŷ` de OLS, SAR y SEM, más
matrices de diagnóstico de heterocedasticidad (Breusch-Pagan, Koenker-Bassett,
Jarque-Bera).

**Evalúa:** autocorrelación remanente en los errores vía test de Moran sobre
residuos. Determina si la varianza del error es constante o si hay fallas
estructurales del modelo global por omisión de variables geográficas (topografía
accidentada, barreras de accesibilidad).

**Aplica:** cartografía de diagnóstico de error. Coropleta de residuos con
clasificación estricta por desviación estándar (`mapclassify.StdMean` /
`StdDeviation`) con cortes simétricos alrededor de 0. Aísla clusters de
sobreestimación (residuos negativos) y subestimación (positivos) para demostrar
visualmente la violación del supuesto de independencia y justificar el paso al
modelado local.

Código: `references/modulo1-residuos.md`.

## Módulo 2 — Parámetros locales y variación multiescala (mgwr)

**Obtiene:** superficies continuas de coeficientes locales `β_i`, errores
estándar locales y el vector de anchos de banda (bandwidths) óptimos
condicionales por variable en GWR/MGWR.

**Evalúa:** la escala operativa de cada proceso socioespacial comparando los
bandwidths. Bandwidth alto (cercano a n) ⇒ proceso global homogéneo. Bandwidth
bajo ⇒ proceso muy localizado y sensible al entorno inmediato (gradiente
topográfico, proximidad a centralidades del área de estudio).

**Aplica:** representación paramétrica multiescala. PROHIBIR suavizar
uniformemente todas las variables. Respetar la rigidez del bandwidth de cada
determinante: variables micro-urbanas con alta variación espacial; variables
globales con gradientes casi planos, evitando sobreinterpretar fluctuaciones
locales artificiales.

**Efectos de borde (Haining, Griffith).** Las observaciones del perímetro tienen
menos vecinos en `W` solo porque el mapa se corta ahí, lo que infla la
incertidumbre en los márgenes de GWR/MGWR. Mitigación técnica: calibrar con un
búfer de datos ~10% más allá del límite oficial y recortar el mapa final al límite
de la tesis. Mitigación visual: acompañar TODA superficie de coeficientes con un
mapa secundario de error estándar local; contornos de error más anchos en la
frontera alertan que esas estimaciones se interpretan con cautela.

**Sobreajuste por ancho de banda (Fotheringham).** No aceptar el bandwidth
automático (AICc) sin auditarlo: con datos ruidosos puede elegir un bw diminuto y
producir un mapa hiper-fragmentado que mapea ruido. Contrastar con el conocimiento
del territorio y preferir un bw de restricción más amplio si el efecto "cambia
cada dos cuadras". El VIF local (skill `control-sesgos-validacion`) complementa
esta auditoría.

Código: `references/modulo2-mgwr.md` y `references/modulo6-efectos-borde.md`.

## Módulo 3 — Algoritmo de máscara de significancia local

**Obtiene:** pseudo p-valores locales o estadístico t por cada `β_i`.

**Evalúa:** umbrales de inferencia (Anselin, Fotheringham). Para 95% de
confianza: `|t_i| >= 1.96` o `p <= 0.05`. Para MGWR, corregir por dependencia
y comparaciones múltiples ajustando el t crítico según los grados de libertad
efectivos (corrección de Da Silva & Fotheringham) — usar el t crítico ajustado
que devuelve `mgwr`, no el 1.96 fijo, cuando esté disponible.

**Pruebas múltiples (Wheeler).** Con n manzanas se corren n pruebas: por azar ~5%
saldrán "significativas" (clústeres fantasma). Aplicar corrección por
comparaciones múltiples — Bonferroni (estricto) o FDR Benjamini-Hochberg
(recomendado) — antes de encender el mapa. La máscara se encoge pero lo que queda
es estructura real. Aplica también a LISA y Gi* del skill `eda-esda-diagnostico`.

**Aplica:** máscara binaria visual sobre el lienzo. Cruzar geometría ×
significancia: si una unidad registra `|t_i| < t_crit`, ANULAR su codificación de
color temática y aplicar filtro neutro — gris plano, `alpha=0`, o tramado lineal
tenue (hatch). El mapa resultante aísla los verdaderos hotspots/coldspots de
impacto econométrico.

Código: `references/modulo3-mascara-significancia.md`.

## Módulo 4 — Codificación cromática divergente y quiebres matemáticos

**Obtiene:** máximo, mínimo y media de los coeficientes locales SIGNIFICATIVOS
que cambian de signo en el territorio (variables que penalizan el valor en la
periferia pero lo incentivan en el centro).

**Evalúa:** la posición exacta del cero dentro del rango. Determina si el rango
es simétrico o asimétrico respecto al punto de inflexión cualitativo.

**Aplica:** paletas divergentes simétricas (`RdBu_r`, `coolwarm`, divergentes de
ColorBrewer) ancladas RÍGIDAMENTE en `0.0` con
`TwoSlopeNorm(vcenter=0)`. Calibrar límites para que el color de transición
(blanco/gris claro) coincida matemáticamente con el cero. Positivos saturados en
un extremo (rojo), negativos en el opuesto (azul), revelando la mutación espacial
del determinante de un vistazo.

Código: `references/modulo4-divergente.md`.

## Módulo 5 — Cartografía del poder explicativo local (R² local)

**Obtiene:** bondad de ajuste local (`localR2`) por punto de calibración o unidad
ponderada del modelo local.

**Evalúa:** la geografía del rendimiento del modelo. Identifica dónde la
especificación explica bien y dónde el R² local cae drásticamente, señalando
zonas con dinámicas diferenciadas o posibles variables omitidas con estructura
espacial propia.

**Aplica:** rampas secuenciales monocromáticas / de luminosidad gradual (`YlGn`,
`Purples`). RESTRINGIR clases amplias; usar intervalos precisos para delimitar las
fronteras de rendimiento. Este mapa de control es OBLIGATORIO junto a cualquier
superficie de coeficientes.

Código: `references/modulo5-r2-local.md`.

## Módulo 7 — Efectos directos, indirectos y totales (SLM / SDM)

Cuando el modelo elegido incluye término autorregresivo `ρWy` (SLM, SDM, SAC),
el coeficiente `β` NO es el efecto marginal real: la retroalimentación espacial
`(I − ρW)⁻¹` amplifica el impacto. Los efectos S(W) de LeSage-Pace descomponen
ese impacto en:

- **Directo**: efecto promedio de una unidad sobre sí misma (diagonal de S(W)).
- **Indirecto (spillover)**: efecto promedio de una unidad sobre sus vecinos
  (fuera de la diagonal de S(W)).
- **Total**: suma de directo e indirecto.

**Regla de cartografía.** Los efectos directos/indirectos son ESCALARES por
variable (no superficies por unidad), por lo que se reportan en tabla, no en mapa.
Incluir en el texto los tres efectos con SE e IC 95% para cada predictor relevante.
Para el cálculo ver: skill `econometria-hedonica-espacial` →
`references/modulo4-efectos.md`.

**En SDM** los efectos del rezago espacial `WX` también se descomponen: reportar
el efecto directo de `X` y el indirecto de `WX` por separado antes de mostrar
las superficies de coeficientes locales.

## Módulo 6 — Representación bivariada (coeficiente + incertidumbre)

Cuando el análisis requiere mostrar simultáneamente la magnitud de un coeficiente
GWR/MGWR **y** su error estándar local (o nivel de significancia), usar un esquema
de color bivariado sequential-sequential en lugar de dos mapas separados.

La combinación recomendada:
- **Eje A (columnas)**: magnitud del coeficiente β_i (clase baja → alta en escala absoluta)
- **Eje B (filas)**: error estándar o incertidumbre (clase baja → alta)

Celda esquina inferior-derecha (alta magnitud + baja incertidumbre) = resultado
robusto y significativo → color más saturado. Celda superior-derecha (alta magnitud
+ alta incertidumbre) = interpretar con cautela → color intermedio/desaturado.

Código y paleta: skill `composicion-lienzo-cartografico` → `references/modulo14-color-bivariado.md`.

**Nota de proyección (Brewer).** TODA superficie de coeficientes debe estar en
proyección de **igual área** (usar el CRS métrico local del área de estudio). Representar
coeficientes sobre Plate Carrée crea gradientes visuales falsos por distorsión
de área en las zonas periféricas. Verificar con `gdf.crs` antes de graficar.

## Entrega final

Para cada variable significativa entregar el par: superficie de coeficiente
(divergente, enmascarada, anclada en 0) + mapa de R² local. Nunca entregar un
mapa de coeficientes sin su máscara ni sin su R² local acompañante. Pasar las
figuras al skill `composicion-lienzo-cartografico` para el acabado y export.
