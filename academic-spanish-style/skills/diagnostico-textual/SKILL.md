---
name: diagnostico-textual
description: >
  Usa este skill cuando el usuario quiera un diagnóstico profundo de un fragmento de
  texto. Disparadores: "qué está mal en este párrafo", "diagnostica este texto", "por
  qué suena desorganizado", "hay problemas de coherencia", "está confuso", "el revisor
  dijo que no se entiende", "necesito saber dónde falla este borrador", "analiza este
  fragmento", "hay muchos errores pero no sé de qué tipo". Aplica la taxonomía de 4
  niveles de Cassany (normativa, cohesión, coherencia, adecuación) priorizando fondo
  sobre forma. Emite feedback balanceado (+/−). Calibrado para textos académicos en
  español (tesis, artículos, reportes de investigación cuantitativa).
metadata:
  version: "0.1.0"
  author: "academic-spanish-style"
  source: "Cassany, D. (1993). Reparar la escritura. Didáctica de la corrección de lo escrito. Editorial Graó, Barcelona."
---

# Diagnóstico Textual — Taxonomía Cassany 4 niveles

Analiza un fragmento y clasifica sus problemas en la jerarquía normativa → cohesión →
coherencia → adecuación. Diagnostica de mayor a menor profundidad (contenido primero,
forma después). Emite feedback con signos + y − equilibrados. No corregir
exhaustivamente: seleccionar 2–4 problemas prioritarios por sesión.

Cargar `taxonomia-errores.md` y `tipos-errores-forma-fondo.md` al iniciar.

---

## Protocolo de diagnóstico

### Paso 1 — Lectura global (antes de marcar nada)

Leer el texto de corrido sin detenerse en errores superficiales. Responder mentalmente:

- ¿Se entiende el propósito global del fragmento?
- ¿Las ideas están ordenadas de forma que el lector las puede seguir?
- ¿El registro es apropiado para el contexto académico?
- ¿El problema más grave es de contenido o de forma?

### Paso 2 — Diagnóstico por niveles (ver `taxonomia-errores.md`)

Aplicar los 4 niveles en orden descendente de prioridad:

**Nivel 4 — ADECUACIÓN** (prioridad máxima)
- ¿El registro es académico-formal y consistente a lo largo del fragmento?
- ¿Hay mezcla de registros (coloquial + formal)?
- ¿El tono es objetivo? ¿Hay subjetivismo no marcado?
- ¿Los giros son propios del género (tesis/artículo científico en español)?
- Etiqueta en salida: `[ADECUACIÓN]`

**Nivel 3 — COHERENCIA** (prioridad alta)
- ¿Las ideas están seleccionadas y son relevantes para el argumento central?
- ¿La progresión temática es lógica (de lo conocido a lo nuevo)?
- ¿Cada párrafo tiene unidad temática (una sola idea principal)?
- ¿La estructura del fragmento tiene una organización reconocible?
- Etiqueta en salida: `[COHERENCIA]`

**Nivel 2 — COHESIÓN** (prioridad media)
- ¿Los nexos y marcadores textuales conectan las ideas entre sí?
- ¿Las anáforas (pronombres, sinónimos, hiperónimos) funcionan sin ambigüedad?
- ¿El orden de los elementos dentro de cada frase es lógico?
- Etiqueta en salida: `[COHESIÓN]`

**Nivel 1 — NORMATIVA** (prioridad baja)
- Ortografía, morfología, sintaxis, puntuación, léxico
- Solo marcar si es un error grave o si los niveles 3–4 ya están resueltos
- Etiqueta en salida: `[NORMATIVA]`

### Paso 3 — Feedback balanceado (ver `tipos-errores-forma-fondo.md`)

Identificar 2–3 aspectos positivos (+) y 2–4 problemas prioritarios (−) antes de
producir la salida. No mezclar comentarios de niveles distintos sin priorizar.

**Regla de selección:** Si hay problemas en Nivel 3–4, no invertir más de 1 comentario
en Nivel 1. Corregir la forma en un texto incoherente es trabajo desperdiciado.

### Paso 4 — Salida estructurada

```
DIAGNÓSTICO TEXTUAL
═══════════════════

PUNTOS FUERTES (+)
[1] ...
[2] ...

PROBLEMAS PRIORITARIOS (−)
[1] [COHERENCIA] ...
[2] [ADECUACIÓN] ...
[3] [COHESIÓN] ...

FRAGMENTO REVISADO
[solo si hay ≤ 2 problemas de Nivel 1–2 y la estructura de Nivel 3–4 está sólida]

EXPLICACIÓN DE CAMBIOS
[para cada cambio: qué se corrigió, por qué, cómo aplicar el criterio
en otros párrafos del mismo texto]
```

---

## Módulo — Distinción error vs. falta

**Error**: desconocimiento de la regla. El autor produce el mismo problema de forma
sistemática porque no sabe que es incorrecto. → Requiere explicación de la regla.

**Falta**: distracción o descuido. El autor conoce la regla pero no la aplicó en ese
momento. → Requiere solo advertencia puntual.

**Diagnóstico rápido:**
- Mismo tipo de problema ≥ 3 veces en el texto → **error** (patrón sistemático)
- 1–2 ocurrencias en texto por lo demás correcto → **falta** (excepción)

---

## Módulo — Corrección selectiva

No corregir todo en un solo pase. Criterios para seleccionar qué señalar:

1. **Gravedad comunicativa**: ¿Impide entender el texto? → Prioridad máxima
2. **Patrón vs. excepción**: ¿Es sistemático? → Explicar la regla, no solo corregir
3. **Momento del proceso**: Borrador temprano → solo Niveles 3–4. Versión para envío → todos los niveles
4. **Lo accionable**: Solo señalar lo que el autor puede corregir con los recursos que tiene disponibles
