---
name: legibilidad-estilo-llano
description: >
  Usa este skill cuando el usuario quiera medir o mejorar la legibilidad de un fragmento
  académico en español. Disparadores: "simplifica este texto", "está muy denso", "revisa
  la legibilidad", "el abstract es difícil de entender", "demasiado técnico para el
  comité", "las frases son muy largas", "reescribe en estilo llano", "muy burocrático",
  "necesito que se entienda a la primera lectura", "simplifica sin perder el contenido",
  "auditoría de legibilidad", "hay demasiados incisos", "la sintaxis es muy compleja".
  Aplica criterios científicos de legibilidad lingüística (Flesch/Henry adaptados al
  español) y los principios del estilo llano. Calibrado para abstract, introducción,
  metodología y conclusiones de tesis y artículos en español.
metadata:
  version: "0.1.0"
  author: "academic-spanish-style"
  source: "Cassany, D. (1993). La cocina de la escritura (Cap. 1, secciones La legibilidad y El estilo llano). Anagrama, Barcelona."
---

# Legibilidad y Estilo Llano — Skill de claridad textual

Analiza la legibilidad de un fragmento aplicando criterios objetivos y produce una versión
de estilo llano con explicación de los cambios. Cargar `criterios-legibilidad.md` y
`transformaciones-llano.md` al iniciar.

---

## Módulo 1 — Diagnóstico de legibilidad (ver `criterios-legibilidad.md`)

### 1.1 Factor 1: Extensión de las oraciones

Contar oraciones y estimar la longitud media en palabras.

| Longitud media | Diagnóstico |
|---|---|
| ≤ 20 palabras | Legibilidad alta |
| 21–30 palabras | Legibilidad media |
| > 30 palabras | Legibilidad baja — candidatas a división |
| > 40 palabras | Legibilidad muy baja — reescritura necesaria |

Identificar todas las oraciones > 35 palabras como intervención prioritaria.

### 1.2 Factor 2: Complejidad léxica

- Identificar palabras de > 3 sílabas que **no son** términos técnicos del dominio
- Distinguir:
  - **Tecnicismo necesario**: "autocorrelación espacial", "heteroscedasticidad" → conservar
  - **Complejidad gratuita**: "manifestación fenoménica" cuando se puede decir "manifestación" → simplificar
- Solo señalar complejidad léxica cuando sea reemplazable sin pérdida de precisión técnica

### 1.3 Factor 3: Abstracción vs. concreción

- ¿El texto trabaja con conceptos abstractos o con datos, ejemplos, objetos concretos?
- Párrafos puramente abstractos sin ancla concreta = baja legibilidad
- Acción: proponer punto de inserción para un ejemplo de 1–2 oraciones

### 1.4 Factor 4: Obstáculos sintácticos

| Obstáculo | Descripción | Umbral crítico |
|---|---|---|
| Inciso excesivo | Fragmento entre comas/paréntesis | > 15 palabras → dividir |
| Subordinación anidada | Subordinada dentro de subordinada | ≥ 2 niveles de anidamiento |
| Sujeto alejado del verbo | Distancia entre sujeto y verbo principal | > 10 palabras → reestructurar |
| Gerundio introductorio acumulado | Múltiples gerundios al inicio de la oración | ≥ 2 → reescribir con verbos conjugados |

### 1.5 Factor 5: Marcadores textuales y guía al lector

- ¿Hay conectores entre ideas (sin embargo, por tanto, en consecuencia, dado que)?
- ¿Hay variación estructural que ayude a segmentar (listas, puntos, dos puntos)?
- Ausencia total de marcadores = riesgo de que el lector no vea las relaciones lógicas

### 1.6 Escala diagnóstica

| Factores problemáticos | Diagnóstico | Acción |
|---|---|---|
| 0–1 | Legibilidad alta | Ajustes menores |
| 2–3 | Legibilidad media | Revisión selectiva (priorizar Factores 1 y 4) |
| 4–5 | Legibilidad baja | Reescritura profunda |

---

## Módulo 2 — Obstáculos prioritarios

Cassany identifica dos obstáculos de comprensión **insalvables para el lector**:

1. **Estructura sintáctica excesivamente compleja**: subordinadas anidadas, períodos
   largos, incisos extensos. El lector pierde el hilo y no puede reconstruirlo solo.

2. **Ausencia de contexto compartido**: texto que asume conocimiento previo que el
   lector no tiene sin proveer el andamio necesario. El lector queda sin punto de apoyo.

**El léxico técnico no es un obstáculo insalvable**: el lector puede buscar un término
en el glosario o la bibliografía. No puede reconstruir una sintaxis rota.

**Consecuencia práctica:** Priorizar simplificación sintáctica sobre simplificación
léxica. Un texto con vocabulario especializado pero frases claras es legible para el
especialista. Un texto con palabras sencillas pero sintaxis enredada no lo es para nadie.

---

## Módulo 3 — Transformación a estilo llano (ver `transformaciones-llano.md`)

### Criterios del escrito académico llano

1. **Lenguaje apropiado al lector y al documento**: calibrar el nivel técnico según la
   audiencia de esa sección específica (abstract vs. metodología vs. conclusiones)
2. **Diseño racional**: datos relevantes al inicio del párrafo/oración; detalles en
   posición secundaria
3. **Comprensible en primera lectura**: si el lector debe retroceder para encontrar el
   sujeto o la cláusula principal, la oración falla
4. **Cumple requisitos del género**: no simplificar hasta perder la precisión técnica
   ni el hedging académico necesario

### Técnicas de transformación

**T1 — Dividir oraciones largas**

Identificar el punto de mayor complejidad de la oración (donde se acumulan las
subordinadas o los incisos) y separar en dos o más oraciones.

**T2 — Adelantar sujeto y verbo**

Regla: `sujeto → verbo → complementos`. Evitar gerundios de apertura acumulados.
```
Antes:  "Habiéndose analizado los residuos y confirmado la ausencia de patrones
         sistemáticos, se procedió a la estimación."
Después: "Los residuos no mostraron patrones sistemáticos. A partir de este
          diagnóstico, se estimó el modelo."
```

**T3 — Eliminar o separar incisos**

Si el inciso entre comas/paréntesis supera 15 palabras: convertir en oración aparte.
Si es decorativo (no necesario para el argumento): eliminar.

**T4 — Sustituir nominalizaciones pasivas por verbos activos**

Aplicar solo cuando no implique pérdida de impersonalidad científica requerida.
```
Antes:  "Se realizó una evaluación de la autocorrelación espacial"
Después: "Se evaluó la autocorrelación espacial"

Antes:  "La implementación del modelo fue efectuada mediante el paquete PySAL"
Después: "El modelo se implementó con el paquete PySAL"
```

**T5 — Agregar contexto concreto tras afirmación abstracta**

Después de cada afirmación abstracta: ¿hay un ejemplo, dato o referencia que la ancle?
Si no, proponer un punto de inserción.

```
Antes:  "La dependencia espacial afecta la estimación de los parámetros."
Después: "La dependencia espacial afecta la estimación de los parámetros. Por
          ejemplo, cuando los residuos del modelo OLS presentan autocorrelación
          positiva (I de Moran significativo), los errores estándar quedan
          subestimados y la inferencia es inválida."
```

---

## Módulo 4 — Salida

```
DIAGNÓSTICO DE LEGIBILIDAD
══════════════════════════
Factores críticos detectados: [lista]
Nivel general: Alto / Medio / Bajo
Longitud media de oración: [n] palabras

VERSIÓN EN ESTILO LLANO
[fragmento revisado]

CAMBIOS APLICADOS
[1] Técnica T[n]: [descripción de qué se cambió y por qué]
[2] ...

ADVERTENCIAS
[si algún cambio podría afectar la precisión técnica o el hedging académico,
indicarlo explícitamente para que el autor verifique]
```
