---
name: jars-methodology-checker
description: >
  Usa este skill cuando el usuario quiera verificar o mejorar su sección de
  Metodología (o Método) contra los estándares APA 7 para investigación
  cuantitativa. Disparadores: "revisa mi metodología", "chequea el método
  contra JARS", "qué falta en mi sección de métodos", "cómo reportar la
  regresión en APA", "cómo describo la muestra en APA 7", "está completa mi
  metodología", "reporta los estadísticos bien", "falta algo en los
  resultados", "audita JARS-Quant", "cómo escribo la sección de análisis".
  Cubre los estándares JARS-Quant completos: diseño, participantes/unidades
  de análisis, instrumentos/variables, procedimiento, análisis estadístico y
  reporte de resultados. Calibrado para investigación cuantitativa con
  componente espacial (modelos SAR/SEM/GWR/MGWR, análisis de flujos,
  clustering espacial, análisis espacio-temporal).
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
  source: "APA Publication Manual 7th ed., Cap. 3 (JARS-Quant), Secs. 3.5-3.12"
---

# JARS Methodology Checker — Auditoría de metodología cuantitativa APA 7

Audita la sección de Metodología/Método contra los Journal Article Reporting
Standards cuantitativos (JARS-Quant). Opera con un checklist exhaustivo:
detecta omisiones, evalúa completitud e indica exactamente qué agregar y dónde.

No pedir al usuario que especifique qué revisar. Leer el texto completo,
aplicar el checklist módulo por módulo y emitir diagnóstico estructurado.

---

## Módulo 1 — Verificación del diseño de investigación

**Checklist obligatorio (JARS-Quant §3.6)**

☐ Tipo de diseño declarado explícitamente (observacional transversal, longitudinal,
  cuasi-experimental, experimental, etc.)
☐ Justificación de por qué ese diseño responde la pregunta de investigación
☐ Hipótesis o preguntas de investigación preclasificadas en:
  - Preplaneadas primarias
  - Preplaneadas secundarias
  - Exploratorias (post-hoc) — *APA 7 requiere distinguirlas explícitamente*
☐ Marco temporal de recolección de datos
☐ Unidad de análisis definida (vivienda, manzana, zona censal, predio)

**Para estudios con datos espaciales — verificar además:**
☐ Ámbito geográfico delimitado con coordenadas o descripción del área de estudio
☐ Período de recolección de datos especificado
☐ Fuente de los datos geoespaciales declarada (fuente institucional, catastro, API, encuesta)
☐ Naturaleza del dato: tipo de medición, escala y unidades de la variable dependiente

**Errores comunes**
- [DISEÑO-IMPLÍCITO] Diseño no declarado explícitamente, solo inferible
- [HIPOTESIS-MEZCLADAS] Hipótesis exploratorias presentadas como preplaneadas
- [UNIDAD-AMBIGUA] Unidad de análisis no definida o cambia entre secciones

---

## Módulo 2 — Verificación de participantes / unidades de análisis

**Checklist (JARS-Quant §3.6 — Participants)**

☐ N total de observaciones/unidades
☐ Método de muestreo declarado (probabilístico, por conveniencia, censal, etc.)
☐ Criterios de inclusión y exclusión explícitos
☐ Procedimiento de reclutamiento o recolección
☐ Tasas de no respuesta o pérdida de datos (attrition)

**Para datos geoespaciales — verificar además:**
☐ Período cubierto por la muestra
☐ Distribución espacial de observaciones (¿cubre el área de estudio uniformemente?)
☐ Tratamiento de duplicados / registros repetidos de la misma unidad
☐ Valores atípicos: criterio de exclusión declarado

**Errores comunes**
- [N-FALTANTE] N no reportado en Método (solo en Resultados)
- [MUESTREO-VAGO] "se recolectaron datos de fuentes secundarias" sin especificar cuáles ni cómo
- [EXCLUSION-AUSENTE] Sin criterios de exclusión explícitos

---

## Módulo 3 — Verificación de variables e instrumentos

**Checklist (JARS-Quant §3.6 — Measures)**

☐ Variable dependiente operacionalizada (precio, log-precio, precio/m², etc.)
☐ Cada variable independiente definida operacionalmente con su fuente
☐ Variables categóricas: categorías de referencia declaradas
☐ Transformaciones aplicadas (log, normalización, winsorización) justificadas
☐ Unidades de medida para todas las variables numéricas
☐ Fuente de datos geoespaciales con resolución y fecha

**Para variables geoespaciales — verificar que el texto cubra las categorías relevantes:**
| Categoría | Ejemplos de atributos |
|-----------|----------------------|
| Variables de la unidad de análisis | atributos propios de cada observación (estructura, capacidad, tamaño) |
| Variables de entorno/vecindad | distancia a centros, densidad, uso de suelo, accesibilidad |
| Variables de contexto territorial | características del área, zonificación, infraestructura |

**Errores comunes**
- [VAR-SIN-FUENTE] Variable geoespacial sin citar la capa/institución de origen
- [CAT-REF-OMITIDA] Variable dummy sin declarar categoría de referencia
- [TRANSFORMACION-INJUSTIFICADA] Log aplicado sin justificación estadística

---

## Módulo 4 — Verificación de procedimiento analítico

**Checklist (JARS-Quant §3.6 — Procedure)**

☐ Software estadístico con versión: `Python 3.x.x`, `R 4.x.x`, `GeoDa x.x`
☐ Paquetes clave con versión: `geopandas`, `spreg`, `linearmodels`, etc.
☐ Secuencia de análisis descrita (EDA → diagnóstico espacial → modelado)
☐ Método de estimación especificado (OLS, SAR, SEM, SLX, GWR…)
☐ Construcción de la matriz de pesos espaciales W: tipo (queen/rook/KNN/distancia),
  parámetros (k, umbral), estandarización (row-standardized)
☐ Pruebas de diagnóstico previas al modelo final (Moran's I en residuos, LM-lag,
  LM-error, árbol de decisión de Anselin-Florax)
☐ Criterio para selección del modelo final
☐ Tratamiento de valores perdidos

**Errores comunes**
- [SOFTWARE-SIN-VERSION] "Se usó Python" sin versión
- [W-SIN-ESPECIFICAR] "matriz de pesos espaciales" sin tipo ni parámetros
- [DIAGNOSTICO-OMITIDO] Modelo espacial seleccionado sin reportar pruebas LM

---

## Módulo 5 — Verificación de reporte de resultados

**Checklist (JARS-Quant §3.7 — Results)**

**Estadísticos descriptivos**
☐ Media, DE, mín, máx para variables continuas clave
☐ Frecuencias/porcentajes para variables categóricas
☐ Matriz de correlaciones o estadísticos bivariados antes del modelo

**Reporte del modelo de regresión (APA 7 estándar)**
☐ Coeficiente B (no estandarizado) con SE o IC 95%
☐ Estadístico t o z con grados de libertad
☐ Valor p con umbral declarado (α = .05 por convención)
☐ R² y R² ajustado para OLS
☐ Log-verosimilitud y AIC/BIC para modelos espaciales
☐ Pseudo-R² de Nagelkerke o equivalente para SAR/SEM
☐ Tamaño del efecto — al menos uno: f², η², OR, o cohen's d según diseño
☐ N en cada tabla de resultados

**Para modelos espaciales — además:**
☐ Rho (ρ) para SAR o lambda (λ) para SEM con SE y significancia
☐ Resultado de Moran's I en residuos del modelo final
☐ Si se usa GWR: bandwith y método de selección (GCV, AIC)

**Errores comunes**
- [P-SIN-UMBRAL] Usar *p* < .05 sin declararlo como umbral en Método
- [SE-O-IC-FALTANTE] Coeficientes sin SE o IC
- [EFECTO-OMITIDO] Modelo significativo sin tamaño del efecto
- [MORAN-RESIDUOS] Modelo espacial sin diagnóstico post-estimación
- [TABLA-SIN-N] Tablas de regresión sin N

---

## Módulo 6 — Auditoría de la sección Discusión (JARS-Quant §3.8)

**Checklist:**

☐ Cada hallazgo principal de Resultados tiene correspondencia en la Discusión
☐ La Discusión interpreta (no repite) los resultados
☐ Se contrastan resultados con al menos 3 fuentes de la literatura
☐ Se explican las divergencias con hallazgos previos
☐ Limitaciones declaradas explícitamente (diseño, muestra, datos, modelo)
☐ Alcance de la generalización delimitado
☐ Implicaciones para práctica o política

**Errores comunes:**
- [DISC-REPITE-CIFRAS] Copiar coeficientes de Resultados sin interpretar su significado
- [DISC-OMITE-LITERATURA] Discusión que no cita ningún trabajo previo
- [DISC-FALTA-LIMITE] Sin declaración explícita de limitaciones
- [DISC-OVERGENERALIZA] "Los resultados aplican a toda ciudad patrimonial…" sin respaldo

---

## Módulo 7 — Verificación de tablas y figuras (Cap. 7)

**Para cada tabla en el texto:**
☐ Número arábigo (`Tabla 1`, no `Tabla I`)
☐ Título en cursiva, sentence case, descriptivo
☐ Solo líneas horizontales (ninguna vertical)
☐ Nota general, específica y de probabilidad en ese orden
☐ Todas las abreviaturas definidas en Nota
☐ N reportado en la tabla o en la Nota
☐ Asteriscos de significancia definidos en Nota

**Para cada figura/mapa:**
☐ Número arábigo (`Figura 1`)
☐ Título en cursiva debajo de la figura (sentence case)
☐ Nota con fuente si es adaptada o reproducida
☐ Para mapas: CRS, fuente cartográfica, escala, norte
☐ Accesible en escala de grises / con patrones diferenciables

**Errores comunes:**
- [TABLA-LINEAS-V] Tabla con bordes verticales
- [TABLA-SIN-NOTA] Abreviaciones sin definir en Nota
- [FIG-SIN-FUENTE] Mapa o figura reproducida sin atribución
- [FIG-NO-ACCESIBLE] Figura solo distinguible por color

---

## Módulo 8 — Salida del diagnóstico

### Formato de reporte

```
## Diagnóstico JARS-Quant

### ✓ Elementos presentes
- [lista de lo que está bien, con localización en el texto]

### ✗ Elementos faltantes o incompletos
[MÓDULO][ERROR] Descripción del problema
→ Qué añadir: texto/tabla/valor específico requerido
✦ Estándar (JARS-Quant §X.X): cita de la regla

### Prioridad de corrección
Alta (invalidan el reporte): [errores críticos]
Media (debilitan la argumentación): [omisiones importantes]
Baja (refinamientos): [ajustes menores]

### Texto sugerido para subsanar [MÓDULO]
[borrador de oración o párrafo concreto para completar la omisión]
```

Cargar: `references/jars-quant-checklist.md`, `references/reporte-estadistico.md`,
`references/efectos-tablas.md`
