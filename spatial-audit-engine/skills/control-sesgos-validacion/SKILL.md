---
name: control-sesgos-validacion
description: >
  Use this skill when the user wants reviewer-grade bias control or validation of
  a spatial model — triggers include "control de sesgos", "multicolinealidad
  local", "VIF local", "los coeficientes GWR oscilan", "validación cruzada
  espacial", "spatial CV", "data leakage / fuga de datos", "block CV", "falacia
  ecológica", "consistencia de escala", "endogeneidad espacial", "variable
  omitida", "¿rezago o error espacial?". Audits local multicollinearity (Local
  VIF), enforces spatial block cross-validation against data leakage, guards
  scale consistency (ecological fallacy), and diagnoses spatial endogeneity /
  omitted-variable bias. Applies across the other skills as a final honesty gate.
  Produces reproducible Python (mgwr, spreg, scikit-learn, momepy).
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill 6 — Control de sesgos y validación

Lo que buscan los revisores metodológicos estrictos: sesgos que la matemática
camufla aunque el código corra sin error. Es la compuerta de honestidad que se
aplica sobre la salida de los otros skills antes de discutir resultados. Genera
código reproducible con el criterio incrustado.

## Principio operativo

Un modelo que corre sin error no es un modelo válido. Antes de discutir un
coeficiente, pasar estas cuatro compuertas. Si una falla, el hallazgo es un
artefacto, no un resultado. Tratar como auditorías obligatorias, no opcionales.

## Compuerta 1 — Multicolinealidad local en GWR/MGWR (Fotheringham, Wheeler)

Dos variables pueden no correlacionar globalmente pero sí dentro de un barrio
(ej. en ladera alta, pendiente fuerte y baja accesibilidad coinciden). La matriz
local se vuelve inestable y los `β_i` oscilan salvajemente (positivo gigante en
una manzana, negativo en la de al lado): artefacto, no efecto.

Regla: NO fiarse solo del R² local. Calcular el VIF local en cada punto de
calibración. VIF local > 5 (laxo: 10) ⇒ encender alerta visual o EXCLUIR ese
sector de la discusión. Aplica a la salida del skill `cartografia-modelos-inferencia`.

Código: `references/modulo1-vif-local.md`.

## Compuerta 2 — Validación cruzada espacial / data leakage

Un train/test aleatorio mete un departamento en train y el del piso de abajo en
test: el modelo "adivina" por vecindad (primera ley de Tobler), no por aprender
dinámicas. Las métricas se ven perfectas y el modelo falla en zonas nuevas
(Spatial Data Leakage).

Regla: para evaluar poder predictivo (Random Forest, XGBoost o cualquier modelo),
usar Spatial Block CV: dividir el territorio en bloques/clusters contiguos,
entrenar en unos sectores y probar en un bloque alejado. Reportar la caída de
desempeño respecto al CV aleatorio como evidencia de leakage.

Código: `references/modulo2-spatial-cv.md`.

## Compuerta 3 — Falacia ecológica y consistencia de escala (Robinson, W.S.)

No deducir el individuo desde el agregado. Un distrito con promedio alto NO
implica que cada predio sea caro; dentro del distrito más barato hay islas de
alto valor por micro-morfología (frente plano, parque, vía histórica).

Regla: mantener la escala analítica coherente. Conclusiones sobre predios
individuales ⇒ modelo a nivel de puntos (micro-datos). Datos agregados por
manzana ⇒ conclusiones sobre manzanas, no lotes. Dejar la limitación EXPLÍCITA en
el texto. Enlaza con el diagnóstico MAUP del skill `eda-esda-diagnostico`.

Código y checklist: `references/modulo3-falacia-ecologica.md`.

## Compuerta 4 — Endogeneidad espacial y variable omitida (Anselin)

Cuando Moran de residuos persiste, la reacción de meter un rezago espacial (SAR)
puede ser una trampa: ¿el precio es alto por contagio real de vecinos, o porque
el barrio comparte una variable omitida (prestigio histórico, seguridad,
estética)? Si se omite, el rezago la absorbe y exagera el peso de la vecindad,
ocultando las causas urbanas reales.

Regla: ANTES de saltar a SAR, exprimir variables morfológicas y de accesibilidad
con `momepy` (conectividad de calles, densidad de cruces, diversidad de usos). Si
el Moran de residuos persiste, usar las pruebas robustas LM (`spreg`) para decidir
científicamente entre Rezago (SAR, interacción de mercado) y Error (SEM, estructura
no medida tratada como ruido que protege los coeficientes principales). Conecta con
el árbol LM del skill `econometria-hedonica-espacial`.

Código: `references/modulo4-endogeneidad.md`.

## Compuerta 5 — Correlación espuria por topografía / spatial drift (Haining, Griffith)

En ciudades de geografía agresiva (pendiente pronunciada, variación altitudinal marcada), dos variables pueden parecer
conectadas solo porque comparten la misma tendencia geográfica de fondo (precio
baja y la informalidad sube ladera arriba: ambas siguen la pendiente y la
accesibilidad). Regla: ANTES de cantar victoria con un coeficiente, detrendizar —
regresar la dependiente contra las coordenadas (X, Y) o la elevación (MDE) y
quedarse con los residuos "limpios" del efecto puramente geográfico. Si la
relación con las variables morfológicas persiste sobre esos residuos, el
determinante es real; si desaparece, era un espejismo de la topografía.

Código: `references/modulo5-detrending.md`.

## Compuerta 6 — MAUP altera los coeficientes (Openshaw): robustez multi-escala

El MAUP no solo deforma la coropleta: cambia los `β` de la regresión. El mismo
determinante puede salir negativo a nivel de lote, más fuerte a nivel de manzana y
hasta cambiar de signo a nivel de distrito (la agregación borra la variación
interna e inventa macro-tendencias). Regla: no concluir con una sola unidad.
Correr el modelo a nivel de manzana y repetirlo con grillas H3 (resolución 8, 9,
10); presentar los coeficientes comparados en tabla. Significativo y estable en
todas las escalas ⇒ hallazgo robusto. Se desmorona al cambiar de escala ⇒
artefacto de zonificación. Enlaza con MAUP de `eda-esda-diagnostico` (modulo1b).

Código: `references/modulo6-maup-multiescala.md`.

## Plan de comunicación (honestidad científica)

- Mostrar las costuras: SIEMPRE el mapa de error estándar local junto a los
  coeficientes GWR/MGWR (ver `cartografia-modelos-inferencia`, efectos de borde).
- No saturar el lienzo: variables secundarias van en small multiples de Tufte o en
  un `seaborn` limpio en anexos (ver `composicion-lienzo-cartografico`).
- Escribir vinculando matemática y territorio: no "el coeficiente de X baja al
  norte", sino "el impacto de la accesibilidad vial cae al norte porque la
  topografía abrupta rompe la red de transporte y aísla el suelo del centro de
  empleo".

## Entrega final

Un reporte de las cuatro compuertas con veredicto (pasa / artefacto / limitación a
declarar) por cada coeficiente o modelo discutido. Ningún hallazgo se discute sin
haber pasado las compuertas que le aplican.
