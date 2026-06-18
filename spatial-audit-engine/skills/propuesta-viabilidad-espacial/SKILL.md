---
name: propuesta-viabilidad-espacial
description: >
  Use this skill when the user is preparing a research proposal / plan de tesis
  (NOT the final thesis) and needs to justify the spatial approach and sample
  viability — triggers include "plan de tesis", "research proposal", "propuesta
  de investigación", "defender mi muestra", "380 datos son pocos", "viabilidad
  del dataset", "justificar econometría espacial al jurado", "figuras para el
  proyecto", "sección metodológica del proyecto". Assembles the four proposal
  figures (sample-coverage dual map with KDE, EDA asymmetry, connectivity-graph
  W, Moran scatterplot), defends the sample size for GWR/MGWR, and provides the
  methodology-section checklist and writing guide. Orchestrates the analysis
  skills; produces reproducible Python and a section outline.
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
---

# Skill 7 — Modo propuesta: viabilidad y justificación espacial

Skill de fase PREVIA: arma la sección metodológica de un plan de investigación
(research proposal), no de la tesis final. El jurado no quiere coeficientes
finales; quiere comprobar que el dataset es viable y que existe un problema
espacial real que justifica el enfoque. Orquesta las técnicas de los otros skills
y genera las figuras + el texto guía.

## Principio operativo

En la propuesta el objetivo es JUSTIFICAR, no concluir. Mostrar viabilidad de la
muestra y la necesidad estadística del enfoque espacial — no resultados del
modelo local. Reusar el código de los skills de análisis; aquí solo se
selecciona, ordena y narra.

## Módulo 1 — Adecuación de la muestra (N variable) y cómo crecerla

El tamaño NO es fijo: hoy es uno y crecerá. No afirmar "N es ideal" de forma
estática; DIAGNOSTICAR la adecuación para el N actual y dar recomendaciones,
óptimos y la ruta para obtener más datos. Tres preguntas:

1. **¿Es computacionalmente viable?** GWR/MGWR invierten matrices iterativamente
   (O(N²)–O(N³)); el punto dulce práctico está en cientos a pocos miles. Decenas
   de miles ⇒ lento/colapsa (mitigar con ESF, ver `control-sesgos-validacion`).
2. **¿Alcanza para calibrar localmente?** Lo decisivo es que cada ventana de
   ancho de banda adaptativo tenga suficientes vecinos: regla de oro local
   ≳ 5–10 obs por parámetro; con anchos adaptativos esto se garantiza fijando un
   k mínimo. Global (OLS hedónico): ≳ 10–15 obs por predictor.
3. **¿Cubre el territorio?** Más importante que N es la COBERTURA (Anselin,
   Fotheringham): NNI, densidad KDE, % del área a < d de un punto, y vacíos por
   zona. N adecuado mal distribuido sigue siendo inválido.

El skill entrega un diagnóstico cuantitativo del N actual, el rango óptimo
recomendado, y una estrategia de adquisición/muestreo para llenar vacíos, más un
monitor para re-evaluar a medida que la muestra crece.

Diagnóstico, óptimos y obtención de datos: `references/modulo1-adecuacion-muestra.md`.

## Módulo 2 — Las cuatro figuras clave de la propuesta

1. **Mapa de cobertura y densidad (viabilidad).** Mapa dual: izquierda, los 380
   puntos crudos con `alpha=0.6`; derecha, superficie KDE. Demuestra que la
   muestra cubre el continuo urbano (centro histórico ↔ San Jerónimo/San
   Sebastián) y no un solo barrio. Código nuevo en
   `references/modulo2-figuras-clave.md`.
2. **EDA de asimetría y submercados.** `jointplot` precio vs. determinante
   principal con histogramas marginales + `violinplot` por distrito. Revela la
   cola larga a la derecha que justifica log + multi-escala. Reusa
   `eda-esda-diagnostico/references/modulo2-eda-distribucion.md`.
3. **Grafo de conectividad topológica (define W).** Líneas KNN (k=4 o 6) entre
   puntos sobre mapa base mudo. Muestra que ya computaste `W` y controlaste islas
   por barreras. Reusa `eda-esda-diagnostico/references/modulo3-pesos-espaciales.md`
   y el mapa base mudo de `composicion-lienzo-cartografico/references/modulo4-contextily.md`.
4. **Diagrama de dispersión de Moran (justifica el problema).** `splot`
   moran_scatterplot; pendiente positiva + p<0.05 ⇒ "el OLS viola independencia,
   el enfoque espacial es necesidad, no capricho". Reusa
   `eda-esda-diagnostico/references/modulo4-moran-global.md` y `modulo4b-lisa.md`.

## Módulo 3 — Checklist y guía de redacción

Orden lógico de la sección metodológica del proyecto:
1. **Datos:** mapa de cobertura (fig. 1) + diagnóstico EDA (fig. 2). Demuestra que
   los 380 datos están limpios y cubren el territorio.
2. **Modelado del espacio:** grafo de conectividad (fig. 3) para definir `W`.
3. **Hipótesis espacial:** Moran scatterplot (fig. 4) como diagnóstico que rompe
   la estadística clásica y abre las hipótesis de heterogeneidad local.
4. **Formulación del modelo:** escribir las ecuaciones del OLS global y plantear
   el MGWR local con anchos de banda ADAPTATIVOS (justificado por la densidad
   variable de la muestra).

Plantillas de texto y checklist: `references/modulo3-checklist-redaccion.md`.

## Entrega final

Cuatro figuras listas para la propuesta + el esqueleto de la sección metodológica
con su narrativa. Para el documento formal (APA 7, Word/PDF), pasar el texto a las
skills de redacción académica disponibles (p. ej. el plugin de academic-paper) o a
la skill `docx`. Confirmar con el usuario el determinante principal y las zonas/
distritos antes de generar las figuras.
