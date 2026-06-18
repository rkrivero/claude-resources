---
name: scholarly-writing-coach
description: >
  Usa este skill cuando el usuario quiera mejorar la prosa de su tesis o
  artículo. Disparadores: "revisa este párrafo", "mejora la redacción",
  "suena muy pasivo", "humaniza este texto", "está muy redundante",
  "demasiado jargon", "cómo suena más académico", "revisa el estilo APA",
  "es muy seco / muy largo / muy vago", "aclara esta oración", "mejora el
  flujo", "corrige la gramática académica", "pulir la prosa", "está bien
  escrito este fragmento". Aplica los principios del Cap. 4 (estilo y
  gramática) y Cap. 6 (mecánica) del Manual APA 7. Calibrado para redacción
  académica en español con terminología técnica de análisis espacial,
  arquitectura y economía urbana.
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
  source: "APA Publication Manual 7th ed., Caps. 4 y 6"
---

# Scholarly Writing Coach — Skill de calidad de prosa académica APA 7

Analiza fragmentos de texto de la tesis y aplica correcciones según los
principios APA 7 de estilo, gramática y mecánica. No pedir confirmación
antes de corregir. Emitir: diagnóstico con errores etiquetados + versión
revisada + explicación de cada cambio.

---

## Módulo 1 — Diagnóstico de continuidad y flujo (APA §4.1–4.3)

**Verificar:**
- Transiciones entre oraciones y párrafos (palabras de enlace: temporal,
  causa-efecto, adición, contraste)
- Strings de sustantivos (*noun strings*): más de 3 sustantivos encadenados
  para modificar uno final → desarmar con preposiciones y verbos
- Párrafos de una sola oración (abruptos) o demasiado largos (> 1 página)
- Consistencia en el uso de términos: no alternar sinónimos que introducen
  ambigüedad (p. ej. "precio de oferta" vs. "precio de lista" vs. "valor
  inmobiliario" refiriéndose al mismo concepto)

**Etiqueta de error:** `[FLUJO]`

**Ejemplo — noun string:**
```
Antes:  "análisis de accesibilidad urbana peatonal multivariado"
Después: "análisis multivariado de accesibilidad peatonal en entornos urbanos"
```

---

## Módulo 2 — Concisión y claridad (APA §4.4–4.6)

**Verificar:**
- Palabras innecesarias (wordiness): frases expandidas que caben en una palabra
- Redundancias léxicas: palabras con el mismo significado en la misma cláusula
- Oraciones demasiado largas con múltiples ideas — proponer separación
- Uso excesivo de nominalizaciones (sustantivos de verbos):
  "se realizó una evaluación de" → "se evaluó"
- Uso de "existe", "hay", "se tiene" como copulativas vacías

**Etiqueta:** `[CONCISION]`

**Tabla de sustituciones frecuentes en tesis de arquitectura:**
| Forma redundante / larga | Forma concisa |
|--------------------------|---------------|
| en el momento actual | actualmente / hoy |
| con el objetivo de | para |
| se llevó a cabo la medición | se midió |
| a nivel de | en |
| en función de | según / conforme a |
| el hecho de que | que |
| tiene la capacidad de | puede |
| se utilizó el uso de | se usó |
| las áreas de estudio que fueron seleccionadas | las áreas de estudio seleccionadas |

---

## Módulo 3 — Tono y registro académico (APA §4.7–4.9)

**Verificar:**
- Contracciones (prohibidas en escritura académica formal)
- Coloquialismos o expresiones informales
- Jargon técnico sin definir en primer uso
- Aproximaciones vagas ("bastante", "muy", "algunos", "ciertos") → cuantificar
  o reemplazar con lenguaje preciso
- Adverbios introductores innecesarios: "Interesantemente,", "Notablemente,"
  → reformular para que la oración lleve el énfasis
- **Neutralidad descriptiva:** sin adjetivación subjetiva, dramática o
  hiperbólica ("sorprendente", "impresionante", "crucial", "innegable",
  "abrumador") → sustituir por el dato verificable que la sostiene
- Contraste profesional, no combativo: "no incorporó X", no "pasó por alto X"

**Etiqueta:** `[TONO]` / `[NEUTRALIDAD]`

---

## Módulo 4 — Voz, tiempo verbal y persona (APA §4.12–4.16)

**Reglas de tiempo verbal (Tabla 4.1 APA 7):**
| Sección | Tiempo recomendado | Ejemplo |
|---------|-------------------|---------|
| Revisión de literatura | Pasado o pretérito perfecto | "Rosen (1974) propuso…" |
| Descripción del método | Pasado | "Se recolectaron 487 registros…" |
| Reporte de resultados | Pasado | "Los coeficientes fueron significativos…" |
| Discusión de implicaciones | Presente | "Los resultados indican que…" |
| Conclusiones / limitaciones | Presente | "La principal limitación es…" |

**Voz activa vs. pasiva:**
- Preferir activa para acciones de los investigadores y participantes
- Pasiva aceptable cuando el objeto es más importante que el actor
  ("Los shapefiles fueron reprojectados a EPSG:32719")
- Señalar sobreuso de pasiva con etiqueta `[VOZ-PASIVA]`

**Persona:**
- Primera persona permitida y recomendada: "analizamos", "identificamos"
- NO tercera persona auto-referencial: "el autor" → "yo" / "nosotros"
- NO "we" editorial (referencia a todos en general) → especificar el sujeto

**Etiqueta:** `[VERBO]`

---

## Módulo 5 — Comparaciones y antropomorfismo (APA §4.10–4.11)

**Comparaciones lógicas:** verificar que los términos comparados sean
del mismo tipo.
```
Incorrecto: "Los precios del CHC son mayores que el distrito de Santiago"
Correcto:   "Los precios del CHC son mayores que los del distrito de Santiago"
```

**Antropomorfismo:** no atribuir acciones humanas a teorías, modelos o datos.
```
Incorrecto: "El modelo concluye que existe autocorrelación"
Correcto:   "Los resultados del modelo indican autocorrelación"
Correcto:   "Concluimos, a partir del modelo, que existe autocorrelación"
```

**Reparto de agencia:** los autores analizan, interpretan, concluyen, proponen;
los modelos y ecuaciones solo estiman, calculan, predicen, arrojan. Emparejar
verbos cognitivos con actores humanos.
```
Incorrecto: "El estudio extrapoló la tasa de crecimiento."
Correcto:   "Extrapolamos la tasa de crecimiento."
```
Aceptadas por uso: "los resultados sugieren", "los datos proporcionan",
"la Tabla 3 muestra".

**Etiqueta:** `[ANTROP]`

---

## Módulo 6 — Mecánica de estilo (APA Cap. 6)

**Números:**
- Palabras para 0–9 en texto corrido; cifras para 10 en adelante
- Siempre cifras: antes de unidades (`3 km`, `5 m²`), estadísticos
  (`M = 4.5`, `p = .03`), porcentajes (`el 8% de los predios`)
- Nunca iniciar oración con cifra → reformular

**Puntuación en estadísticos:**
- Punto decimal (no coma) en estadísticos: `p = .043`, `R² = .67`
- Cero antes del decimal si el rango es 0–1: `r = .54`, `p = .002`
- Sin cero antes si el valor no puede superar 1: `p = .04`, `β = .31`
- Espacio entre estadístico y valor: `t(df) = 3.21`, `F(2, 485) = 12.4`

**Abreviaciones:**
- Definir en primer uso: "Centro Histórico del Cusco (CHC)"
- No usar abreviatura antes de definirla
- No abreviar en títulos de secciones

**Etiqueta:** `[MECANICA]`

---

## Módulo 7 — Triage rápido de párrafo (checklist en 60 segundos)

Ante cualquier fragmento, verificar en este orden de frecuencia de error:

1. **¿Tiene oración tópica?** El párrafo debe abrir con una afirmación, no con datos.
2. **¿Hay "a nivel de"?** → eliminar siempre.
3. **¿Hay nominalizaciones burocráticas?** (`se procedió a`, `se llevó a cabo`) → verbalizar.
4. **¿Hay más de 3 sustantivos encadenados?** → desarmar noun string.
5. **¿El tiempo verbal coincide con la sección?** (pasado en Método/Resultados, presente en Discusión).
6. **¿Hay voz pasiva en una conclusión del investigador?** → convertir a activa.
7. **¿Hay adverbios introductores?** (`Interesantemente`, `Notablemente`) → restructurar.
8. **¿Cifras al inicio de oración?** → reformular.
9. **¿Hay "muy", "bastante", "bastante significativo"?** → cuantificar o eliminar.
10. **¿Hay anthropomorfismo?** (`el modelo concluyó`, `los datos afirman`) → corregir.

---

## Módulo 8 — Estructura interna de párrafo

Ver `references/estructura-discursiva.md` para la anatomía completa.

Patrón de párrafo de Resultados bien construido:
```
[Oración tópica: qué muestra este resultado y su dirección]
[Estadístico: el valor concreto con formato APA]
[Contextualización: comparación con otro grupo, período o umbral]
[Transición: enlace con el siguiente párrafo / resultado]
```

Patrón de párrafo de Discusión bien construido:
```
[Oración tópica: la interpretación, no la repetición del dato]
[Contraste con literatura: quién confirma o contradice]
[Explicación mecanicista: por qué podría ocurrir esto en el CHC]
[Implicación: qué consecuencia práctica o teórica tiene]
```

---

## Módulo 9 — Detección de prosa generada por IA que debe humanizarse

Señales de prosa LLM que requieren intervención manual en una tesis:

| Patrón | Problema | Corrección |
|--------|----------|-----------|
| "Es importante destacar que…" | Filler de apertura | Eliminar; empezar con la afirmación |
| "En el contexto de la presente investigación…" | Circunlocución | "En este estudio…" o directo al punto |
| "Como se puede observar en la Tabla X…" | Señal pasiva y obvia | "La Tabla X revela que…" o integrar el dato |
| Tres frases seguidas con la misma estructura sintáctica | Monotonía LLM | Variar longitud y estructura de oración |
| "Cabe señalar que…" / "Resulta pertinente mencionar…" | Padding | Eliminar la muletilla; decir lo que hay que decir |
| Listas de 3 elementos con punto y coma sin análisis | No sintetiza | Convertir a prosa argumentativa |

---

## Módulo 10 — Salida del coaching

### Formato de respuesta

```
## Diagnóstico de redacción

**Texto original:**
[reproducir el fragmento]

**Triage (checklist de 10 puntos):**
[marcar con ✓ los que pasan, con ✗ los que fallan]

**Errores detectados:**
[TIPO] oración X: descripción del error

**Texto revisado:**
[versión corregida — misma información, mejor expresada]

**Cambios realizados:**
1. [TIPO] "texto antes" → "texto después" — Regla: APA §X.X / principio aplicado
```

Si el texto pasa todos los módulos sin observaciones: declarar
`✓ Sin observaciones — el párrafo cumple los estándares de redacción APA 7`
y señalar específicamente qué lo hace fuerte (estructura, transiciones, precisión).

Cargar: `references/estilo-gramatica.md`, `references/mecanica-estilo.md`,
`references/estructura-discursiva.md`

**Integración opcional:** para diagnóstico profundo de vicios gramaticales del
español (gerundios, dequeísmo, nominalizaciones, pleonasmos, leísmo, lenguaje
inclusivo), activar también el skill `spanish-academic-style`. Los dos skills
son complementarios: este cubre la capa APA 7 (tiempos verbales, voz, mecánica,
prosa LLM); `spanish-academic-style` cubre la capa del castellano académico.
