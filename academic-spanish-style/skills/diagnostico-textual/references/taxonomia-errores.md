# Taxonomía de errores textuales — Cassany (1993)

Fuente: Cassany, D. (1993). *Reparar la escritura. Didáctica de la corrección de lo escrito* (Cap. 2). Editorial Graó, Barcelona.

---

## Los 4 niveles de análisis

### NORMATIVA (nivel superficial — prioridad baja)

Corrección gramatical y ortográfica de unidades por debajo de la oración.

| Subcategoría | Descripción |
|---|---|
| Ortografía | Acentuación, uso de mayúsculas, grafías (b/v, h, s/c/z...) |
| Morfología y sintaxis | Concordancia género/número, conjugación, orden sintáctico |
| Léxico | Barbarismos, imprecisión léxica, calcos del inglés u otras lenguas |
| Puntuación | Signos de puntuación, uso de mayúsculas en inicio de frase |

### COHESIÓN (nivel textual superficial — prioridad media)

Mecanismos que unen las oraciones entre sí dentro del texto. Opera *entre* frases.

| Subcategoría | Descripción |
|---|---|
| Nexos | Marcadores textuales, conjunciones, conectores (sin embargo, por tanto, en consecuencia, además, aunque, dado que...) |
| Anáforas | Pronombres, sinónimos, hiperónimos, elisiones que retoman referentes previos sin ambigüedad |
| Otros | Orden de elementos en la frase; tiempos verbales para coherencia temporal; uso de determinantes |

### COHERENCIA (nivel semántico-organizativo — prioridad alta)

Organización lógica de las ideas y su progresión. Opera sobre el *contenido*.

| Subcategoría | Descripción |
|---|---|
| Selección de información | Ideas claras y relevantes para el argumento; eliminación de digresiones o información irrelevante |
| Progresión de la información | Orden lógico del desarrollo; estructura tema→rema (de lo conocido a lo nuevo) |
| Estructura del texto | Partes reconocibles (introducción, desarrollo, cierre); proporciones adecuadas entre secciones |
| Estructura del párrafo | Cada párrafo = una idea principal; extensión apropiada; unidad interna |

### ADECUACIÓN (nivel pragmático-comunicativo — prioridad máxima)

Adaptación del texto a la situación comunicativa concreta: género, audiencia, propósito.

| Subcategoría | Descripción |
|---|---|
| Selección de variedad | Estándar académico vs. dialectal; evitar coloquialismos, informalidades |
| Selección de registro | Formal/informal, objetivo/subjetivo; mantener el registro de forma consistente |
| Giros estilísticos | Fórmulas propias del género académico: hedging, voz pasiva donde corresponde, nominalizaciones técnicas apropiadas |

---

## Jerarquía de prioridad en corrección

```
ADECUACIÓN  ←  diagnosticar primero (más profundo: propósito comunicativo)
    ↓
COHERENCIA  ←  segunda prioridad (organización del contenido)
    ↓
COHESIÓN    ←  tercera prioridad (conexión entre oraciones)
    ↓
NORMATIVA   ←  diagnosticar último (más superficial: gramática y ortografía)
```

**Principio de Cassany:** La corrección tradicional invierte esta jerarquía: concentra
el trabajo en normativa y descuida coherencia y adecuación. La corrección procesal
respeta la pirámide: fondo antes que forma.

---

## Lo que no se corrige en el contexto académico

Por convención del proceso de revisión textual, no son objeto de diagnóstico en este
marco:

- **Caligrafía o tipografía personal**: estética visual del manuscrito
- **Postura ideológica**: las opiniones del autor, no cómo las expresa
- **Estilo personal idiosincrásico**: giros que no impiden la comunicación y son
  consistentes con el estilo del autor
- **Decisiones científicas**: qué variables incluir, qué modelo usar — pertenecen
  al JARS-checker, no al diagnóstico textual

---

## Ejemplo de aplicación — fragmento de tesis

**Texto original:**
> "Los resultados muestran patrones interesantes. El análisis espacial fue realizado con
> PySAL. Existe una correlación. En este sentido, se puede decir que los datos son
> relevantes para el estudio y que el índice de Moran fue de 0.47 (p < 0.01)."

**Diagnóstico:**

| Nivel | Problema | Etiqueta |
|---|---|---|
| Coherencia | El párrafo mezcla 4 ideas sin relación entre sí; no hay una idea principal | `[COHERENCIA]` |
| Cohesión | "En este sentido" no conecta con nada; la referencia de "los datos" es ambigua | `[COHESIÓN]` |
| Adecuación | "Patrones interesantes" es vago e informal para un resultado | `[ADECUACIÓN]` |
| Normativa | "Existe una correlación" — ¿qué correlación? — no es error gramatical pero sí imprecisión léxica grave | `[NORMATIVA]` |

**Prioridad de intervención:** Primero reescribir el párrafo para que tenga una idea
principal (COHERENCIA), luego ajustar el lenguaje (ADECUACIÓN), luego los conectores
(COHESIÓN), finalmente la precisión léxica (NORMATIVA).
