---
name: revision-por-capas
description: >
  Usa este skill cuando el usuario tenga un borrador y quiera una revisión profunda y
  estructurada. Disparadores: "revisa este borrador completamente", "tengo una versión
  preliminar, ayúdame a revisarla", "necesito una revisión experta", "haz una revisión
  completa del capítulo", "tengo el primer borrador listo", "revisa de fondo y forma",
  "cómo mejoraría un experto este texto", "necesito feedback completo antes de
  enviarlo al asesor", "qué cambiaría en este texto", "revisión profunda de esta
  sección". Implementa el patrón de revisión del escritor experto (Flower, 1989) en
  dos pases: contenido primero (coherencia, adecuación), forma después (cohesión,
  normativa). Calibrado para borradores de tesis y artículos de investigación en español.
metadata:
  version: "0.1.0"
  author: "academic-spanish-style"
  source: "Cassany, D. (1993). Reparar la escritura (Caps. 1–3). Editorial Graó, Barcelona."
---

# Revisión por Capas — Skill de revisión experta multi-pase

Ejecuta una revisión estructurada en dos pases siguiendo el patrón del escritor experto:
contenido y propósito antes que gramática y ortografía. Cargar `experto-vs-novato.md` y
`pases-revision.md` antes de responder.

---

## Principio fundamental (Cassany, 1993, citando a Flower, 1989)

**El escritor aprendiz** revisa palabra a palabra, buscando errores, haciendo cambios
locales e inmediatos. Se concentra en la forma (ortografía, gramática). No tiene una
imagen clara del objetivo retórico del texto.

**El escritor experto** compara el texto real con sus objetivos retóricos, trabaja con
fragmentos extensos, hace cambios globales primero. Solo va a la forma cuando el
contenido está asentado.

**Consecuencia práctica:** Revisar la forma antes de que el contenido esté claro es
trabajo desperdiciado. Un párrafo perfectamente escrito que luego se elimina por
irrelevante o que se reescribe por incoherente es tiempo perdido.

---

## Pase 1 — Revisión de contenido

**Objetivo del Pase 1:** Verificar que el texto dice lo que el autor quiere decir,
en el orden más efectivo para el argumento.

**Cómo ejecutar:** Leer el texto completo de corrido antes de marcar nada. Formular
mentalmente: "¿Cuál es el argumento central de este fragmento?"

### Preguntas guía del Pase 1 (ver `pases-revision.md`)

**Adecuación (pragmático-comunicativo):**
- ¿El registro es académico-formal y consistente?
- ¿Hay mezcla de registros (coloquial + formal)?
- ¿El tono es objetivo? ¿Hay subjetivismo no marcado?
- ¿Las convenciones del género (tesis/artículo) se respetan?

**Coherencia (semántico-organizativo):**
- ¿Puedo identificar la idea principal de cada párrafo en 1 oración?
- ¿El orden de los párrafos es el más efectivo para el argumento?
- ¿Hay párrafos con más de una idea principal? (→ dividir)
- ¿Hay ideas que pertenecen a otro lugar del texto? (→ mover)
- ¿Hay ideas ausentes que el argumento necesita? (→ agregar)
- ¿La progresión va de lo conocido a lo nuevo?
- ¿Cada afirmación tiene su evidencia/referencia correspondiente?

### Salida del Pase 1

```
PASE 1 — CONTENIDO
══════════════════

Argumento central identificado: [1 oración]
Estado: CLARO / PARCIALMENTE CLARO / NO IDENTIFICABLE

Problemas detectados:
[1] [COHERENCIA] ...
[2] [ADECUACIÓN] ...

Cambios recomendados:
- [acción concreta: mover / eliminar / agregar / reformular]
- ...

→ Decisión: continuar con Pase 2 / volver a preedición
```

**Condición para continuar:** Si el Pase 1 requiere reescritura estructural (mover
bloques, eliminar/agregar párrafos), resolver eso primero antes de pasar al Pase 2.

---

## Pase 2 — Revisión de forma

**Condición de entrada:** El contenido del Pase 1 está resuelto o no requiere cambios
estructurales.

**Cómo ejecutar:** Ahora sí, fragmento por fragmento.

### Preguntas guía del Pase 2

**Cohesión:**
- ¿Las oraciones están conectadas con marcadores textuales apropiados?
- ¿Las anáforas (pronombres, sinónimos) son inequívocas?
- ¿El orden de los elementos dentro de las frases es lógico?

**Legibilidad** (aplicar criterios de `legibilidad-estilo-llano`):
- ¿Hay oraciones > 30 palabras? → dividir
- ¿Hay incisos > 12 palabras? → convertir en oración aparte
- ¿Hay nominalizaciones pasivas reemplazables sin pérdida de precisión?
- ¿Hay strings de sustantivos (3+ encadenados)?

**Normativa:**
- Ortografía: signos diacríticos, mayúsculas, grafías
- Morfología: concordancia género/número, tiempos verbales
- Puntuación: comas, punto y coma, dos puntos, punto final

### Salida del Pase 2

```
PASE 2 — FORMA
══════════════

Problemas detectados:
[1] [COHESIÓN] ...
[2] [LEGIBILIDAD] ...
[3] [NORMATIVA] ...

Fragmento revisado:
[versión con cambios aplicados]

Cambios explicados:
[lista numerada con justificación de cada cambio]
```

---

## Protocolo de decisión antes de comenzar

| Situación | Acción |
|---|---|
| El argumento central no es identificable | Volver a `estrategias-preescritura` — reescribir desde las ideas |
| El argumento existe pero el orden es inefectivo | Reestructurar (mover bloques) antes de editar frases |
| La estructura es buena pero la prosa es deficiente | Pase 2 directo |
| El texto está casi listo, solo errores de superficie | Solo normativa y legibilidad |
| Duda entre reestructurar o editar | Siempre reestructurar primero — la edición de un texto mal estructurado es trabajo desperdiciado |

---

## Anti-patrones de revisión novato (a evitar)

1. **Revisión lineal palabra a palabra** — Produce cambios locales que no resuelven
   problemas globales. Señal: el texto queda sin errores ortográficos pero sigue siendo
   difícil de entender.

2. **Forma antes que fondo** — Corregir ortografía en un párrafo que luego se elimina
   o reescribe por incoherente. Trabajo desperdiciado.

3. **Resolver inmediatamente** — Cuando se detecta un problema, corregirlo de inmediato
   sin diagnosticar la causa raíz. El experto pausa, diagnostica y planifica antes de actuar.

4. **Un solo pase de revisión** — Creer que una sola lectura es suficiente. El experto
   revisa el mismo texto múltiples veces con focos distintos.

5. **Revisar sin objetivo retórico claro** — No saber "qué debería decir este texto"
   antes de revisarlo. El experto compara el texto con sus objetivos; el aprendiz
   revisa el texto en el vacío.

---

## Vinculación con otros skills

| Situación detectada en la revisión | Skill complementario |
|---|---|
| El argumento no existe o no está claro | `estrategias-preescritura` |
| La prosa es muy densa o sintácticamente compleja | `legibilidad-estilo-llano` |
| Hay problemas en múltiples niveles (normativa, cohesión, coherencia) | `diagnostico-textual` |
| Hay errores APA de cita o formato | `apa-citation-auditor` (apa7-guidelines) |
| El texto no sigue las convenciones del género académico español | `corrector-prosa-castellana` (este plugin) |
