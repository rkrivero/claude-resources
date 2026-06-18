---
name: spanish-academic-style
description: >
  Skill especializado en vicios del español académico que van más allá del
  Manual APA 7. Disparadores: "suena torpe / pesado / cargado", "tiene
  gerundios raros", "demasiados sustantivos", "muy nominal", "hay dequeísmo",
  "el relativo está raro", "cuyo incorrecto", "lenguaje sexista", "párrafos
  muy largos", "cómo lo hago más ágil", "tiene muletillas", "está redundante",
  "el tono es demasiado formal", "suena a texto de IA traducido del inglés",
  "cómo mejorar el léxico". Cubre: economía léxica, vicios gramaticales del
  castellano, sintaxis y cohesión, lenguaje libre de sesgos. Puede activarse
  de forma autónoma o complementar al `scholarly-writing-coach` cuando el
  diagnóstico APA 7 ya está hecho y se necesita una revisión más profunda de
  la prosa en español.
metadata:
  version: "1.0.0"
  author: "cusco-estructura"
  source: "Manuales de estilo del español académico — Fundéu, RAE, Martínez de Sousa"
---

# Spanish Academic Style — Diagnóstico de prosa académica en español

Analiza fragmentos de texto de la tesis y detecta vicios gramaticales,
léxicos y sintácticos propios del castellano académico. Opera como corrector
sin filtros: etiqueta errores, ofrece versión corregida y explica la regla.

No pedir permiso antes de corregir. No omitir errores por cortesía.

Para transformaciones modelo (antes/después) calibradas al dominio de la tesis
—econometría espacial, mercado inmobiliario de Cusco— consultar
`references/banco-casos.md`: cinco casos resueltos que cubren frases-cascada,
hipérbaton, comodines, párrafos huérfanos y antropomorfismo metodológico.

---

## Módulo 1 — Economía del lenguaje

**Buscar y eliminar:**

### 1a. Estilo nominal (nominalizaciones)
Exceso de sustantivos abstractos que desplazan a los verbos.

| Estilo nominal (débil) | Estilo verbal (fuerte) |
|------------------------|------------------------|
| En el caso de una excesiva preocupación por… | Si los estudiantes se preocupan excesivamente por… |
| Se realizó una evaluación de los predios | Se evaluaron los predios |
| Se procedió a la identificación de | Se identificaron |
| La concretización del análisis permitió | Al concretar el análisis, se pudo |
| La obtención de los datos fue posible mediante | Los datos se obtuvieron mediante |
| Llevó a cabo la medición de | Midió |

**Etiqueta:** `[NOMINAL]`

### 1b. Verbos comodín — sustituir por verbos precisos

| Comodín | Verbo exacto |
|---------|-------------|
| hacer un informe | elaborar un informe |
| hacer público | publicar |
| hacer entrega | entregar |
| dar comienzo | comenzar / iniciar |
| dar lectura | leer |
| tener una enfermedad | padecer una enfermedad |
| poner en marcha | implementar / lanzar |
| tener en cuenta | considerar / contemplar |
| llevar a cabo | ejecutar / realizar / aplicar (según contexto) |

**Etiqueta:** `[COMODIN]`

### 1c. Verbos con sufijo -izar / -ificar innecesarios

| Incorrecto | Correcto |
|------------|---------|
| concretizar | concretar |
| culpabilizar | culpar |
| recepcionar | recibir |
| influenciar | influir |
| posicionar (como postura) | tomar postura |
| visibilizar (cuando existe "mostrar") | mostrar / evidenciar |

**Etiqueta:** `[SUFIJO-IZ]`

### 1d. Muletillas y locuciones vacías

| Eliminar / sustituir | Por |
|----------------------|-----|
| a nivel de | en / entre / para (según contexto) |
| en base a | basándose en / con base en / a partir de |
| de cara a (finalidad) | para / con vistas a |
| al objeto de | para / con el objeto de |
| de alguna manera | [eliminar o especificar cómo] |
| el tema de / el proceso de | [eliminar y pasar directamente al sustantivo] |
| en el sentido de que | [reformular directamente] |
| bajo el punto de vista | desde el punto de vista |
| a nivel de profesores | entre los profesores |

**Etiqueta:** `[MULETILLA]`

---

## Módulo 2 — Vicios gramaticales específicos del español

Ver `references/vicios-gramaticales.md` para la tabla completa con reglas y ejemplos.

### 2a. Gerundio de posterioridad `[GERU-POST]`
El gerundio describe acción anterior, simultánea o consecuencia inmediata.
NO para acciones posteriores en el tiempo:

```
Mal:  "El investigador presentó los resultados, siendo publicados meses después."
Bien: "El investigador presentó los resultados, que fueron publicados meses después."
```

### 2b. Gerundio adjetivo de cosa inanimada `[GERU-ADJ]`
El gerundio NO califica a un sustantivo inanimado:

```
Mal:  "Se promulgó una ley regulando los precios del suelo."
Bien: "Se promulgó una ley que regula los precios del suelo."

Mal:  "Un modelo conteniendo 12 variables explicativas."
Bien: "Un modelo con 12 variables explicativas."
```

### 2c. Galicismo "a + infinitivo" `[GALIC-A-INF]`
En español: "problemas **que** resolver", NO "problemas **a** resolver":

```
Mal:  "Las variables a controlar son..."
Bien: "Las variables que se deben controlar son..."
Bien: "Las variables por controlar son..."
```

### 2d. Dequeísmo `[DEQUEISMO]`
No añadir *de* ante *que* cuando la oración subordinada es sujeto o CD:

```
Mal:  "Pienso de que el modelo es adecuado."
Bien: "Pienso que el modelo es adecuado."
Mal:  "Me alegra de que los resultados sean significativos."
Bien: "Me alegra que los resultados sean significativos."
```

*Prueba:* sustituye la cláusula *que…* por *esto* o *eso*. Si funciona sin *de*, es dequeísmo.

### 2e. Pronombre "quien" para cosas `[QUIEN-COSA]`
*Quien/quienes* solo para personas. Para cosas: *que*, *el cual*, *la cual*:

```
Mal:  "El comité, quien tomará la decisión..."
Bien: "El comité, que tomará la decisión..."

Mal:  "La ciudad a quien fuimos..."
Bien: "La ciudad a la que fuimos..."
```

### 2f. Falta de "cuyo" — uso incorrecto `[CUYO]`
"Que su" no existe en español formal; usar el relativo posesivo *cuyo*:

```
Mal:  "Un modelo que su R² supera el .50..."
Bien: "Un modelo cuyo R² supera el .50..."
```

### 2g. Pleonasmos y redundancias `[PLEON]`
Palabras que repiten información ya implícita:

| Evitar | Porque |
|--------|--------|
| accidente fortuito | todo accidente es fortuito |
| prever con antelación | prever ya implica antelación |
| colofón final | colofón ya es lo final |
| reiniciar de nuevo | re- ya indica repetición |
| volver a reincidir | ídem |
| actualmente en vigor | si está en vigor, es actual |
| totalmente gratis | gratis ya es total |
| hacer especial hincapié | hincapié ya es especial |

### 2h. Anglicismos y falsos amigos `[ANGLICISMO]`
Calcos frecuentes del inglés científico (ver `references/vicios-gramaticales.md`):

| Calco | Español |
|-------|---------|
| reportar | informar / registrar / presentar |
| evidencia (como dato) | pruebas / hallazgos / resultados |
| soportar (una hipótesis) | respaldar / sustentar / validar |
| controlar por | controlar en función de / aislar el efecto de |
| el modelo es capaz de | el modelo permite / capta / estima |

---

## Módulo 3 — Sintaxis: longitud y estructura de frase

Ver `references/sintaxis-frase.md` para los umbrales completos y el orden canónico.

**Umbrales de referencia:**
| Parámetro | Valor |
|-----------|-------|
| Longitud media de oración | 15–25 palabras |
| Máximo absoluto | 30 palabras |
| Inciso máximo | 15 palabras |
| Niveles de subordinación | 2 máximo |

### 3a. Longitud de oración `[LONG-FRASE]`
Media de **15–25 palabras**; máximo absoluto **30**. Si supera 30, fragmentar
con punto y seguido.

```
Pesada (45 palabras):
"El análisis de regresión espacial que se llevó a cabo mediante el software
 Python con el módulo spreg, utilizando una matriz de pesos queen de primer
 orden, estandarizada por fila, reveló que la distancia al CHC era la
 variable más influyente en la determinación de los precios."

Dividida:
"Se estimó una regresión espacial en Python (spreg) con matriz queen de
 primer orden, estandarizada por fila. La variable más influyente resultó
 ser la distancia al CHC."
```

### 3b. Posición de la información clave `[POSICION]`
El comienzo de la frase es la posición más importante. El sujeto y el verbo
deben aparecer pronto:

```
Débil:  "En el contexto del proceso de urbanización acelerada que ha
         experimentado la ciudad en las últimas décadas, los precios han subido."
Fuerte: "Los precios han subido como resultado de la urbanización acelerada
         de las últimas décadas."
```

### 3c. Inciso entre sujeto y verbo `[INCISO-SV]`
Evitar separar sujeto y verbo con un inciso largo:

```
Problemático:
"Los predios ubicados en el Centro Histórico del Cusco, declarado Patrimonio
 de la Humanidad por la UNESCO en 1983 por su excepcional valor histórico
 y arquitectónico, presentan precios un 38% superiores."

Mejor (inciso al final):
"Los predios del Centro Histórico presentan precios un 38% superiores — área
 declarada Patrimonio de la Humanidad por la UNESCO en 1983."
```

### 3d. Coma entre sujeto y verbo `[COMA-SV]`
Error sintáctico grave, aunque al hablar haya una pausa natural:

```
Mal:  "La regresión espacial, reveló autocorrelación significativa."
Bien: "La regresión espacial reveló autocorrelación significativa."
```

### 3e. Adverbios -mente acumulados `[ACUM-MENTE]`
Máximo uno por frase. Si hay dos, solo el último lleva la terminación:

```
Mal:  "El modelo estima pura y simplemente la capitalización patrimonial."
Bien: "El modelo estima pura y simplemente la capitalización patrimonial."
       ↑ correcto si solo son dos; si hay tres, reformular con sustantivo

Mal:  "Actualmente, curiosamente, los precios han caído significativamente."
Bien: "En la actualidad, y de manera llamativa, los precios han caído de forma
       significativa."
```

### 3f. Redacción en positivo `[NEGACION]`
Las frases negativas exigen más esfuerzo cognitivo al lector:

```
Negativo:  "El modelo no muestra independencia espacial en los residuos."
Positivo:  "Los residuos exhiben dependencia espacial significativa."

Negativo:  "No es posible ignorar que la accesibilidad influye en los precios."
Positivo:  "La accesibilidad influye de forma clara en los precios."
```

### 3g. Orden canónico y focalización inicial `[ORDEN]`
Respetar el orden **Sujeto + Verbo + CD + CI + Circunstanciales**. El núcleo
informativo (sujeto + verbo) debe aparecer en las primeras **4 palabras**; los
circunstanciales largos van al final.

```
Núcleo tardío:  "En el contexto del análisis realizado, la distancia al CHC
                 resultó la variable más influyente."
Núcleo temprano: "La distancia al CHC fue la variable más influyente."
```

### 3h. Profundidad de subordinación `[SUBORD]`
Máximo **2 niveles** de subordinación por enunciado. No encadenar relativas
(que… que… que); una tercera cláusula anidada obliga a fracturar con punto.

```
Tres niveles: "El modelo, que incluye variables que capturan atributos que los
               compradores valoran, mostró buen ajuste."
Fracturado:   "El modelo incluye variables de los atributos que valoran los
               compradores. Su ajuste fue bueno (R² = .52)."
```

---

## Módulo 4 — Tono y registro académico

### 4a. Palabras largas vs. palabras sencillas `[VOCAB-LARGO]`

| Pedante | Sencillo |
|---------|---------|
| aproximativo | aproximado |
| parágrafo | párrafo |
| finalizar | concluir / terminar |
| realizar (cuando = hacer) | hacer / ejecutar |
| implementar (cuando = aplicar) | aplicar |
| evidenciar (cuando = mostrar) | mostrar / revelar |

### 4b. Cacofonías y asonancias `[CACOFONÍA]`
Repetición involuntaria de sílabas o terminaciones cercanas:

```
Mal:  "La localización de la localidad localizada en…"
Mal:  "Actualmente, ciertamente, el mercado inmobiliario funciona eficientemente."
```

Solución: variar el léxico, reorganizar la oración.

### 4c. Coloquialismos disfrazados `[COLOQ]`

| Coloquial | Académico |
|-----------|----------|
| se ve que | se observa que / los datos indican que |
| hay que tener en cuenta | es necesario considerar |
| cosas como | aspectos como / variables como |
| bastante significativo | estadísticamente significativo (p < .05) |
| muy alto | elevado / superior a [valor] |
| a día de hoy | actualmente / en la actualidad |

---

## Módulo 5 — Lenguaje libre de sesgos (español)

Ver `references/lenguaje-inclusivo.md` para la guía completa.

### 5a. Masculino genérico `[GENERO]`
Alternativas al masculino como forma única:

| Masculino genérico | Alternativa neutra |
|--------------------|-------------------|
| el investigador | la persona investigadora · quien investiga · el equipo de investigación |
| los estudiantes | el estudiantado · las personas estudiantes |
| los ciudadanos de Cusco | la ciudadanía cusqueña · los habitantes de Cusco |
| el arquitecto que diseñó | quien diseñó · la persona responsable del diseño |

**Norma institucional (RAE):** preferir sustantivos colectivos y giros neutros;
el masculino genérico gramatical es aceptable. Desdoblamiento con moderación.
**Prohibido** alterar la morfología con `/`, `@` o `-e` (`l@s`, `les`, `niñes`).
Ver `references/lenguaje-inclusivo.md`.

### 5b. Persona-first en español `[PERSONA-FIRST]`

| Etiqueta reduccionista | Persona-first |
|------------------------|--------------|
| los pobres | las personas en situación de pobreza |
| los discapacitados | las personas con discapacidad |
| los desplazados | las personas en situación de desplazamiento |
| los informales | los trabajadores del sector informal |

### 5c. Especificidad en vez de generalización `[ESPECIFICIDAD]`
Mencionar características (edad, nivel socioeconómico, origen étnico) solo
cuando son directamente relevantes para el análisis. Nunca como decoración.

### 5d. Esencialismo — evitar grupos monolíticos `[ESENCIAL]`

```
Esencialista:  "La cultura andina valora la comunidad sobre el individuo."
Específico:    "En las comunidades estudiadas de Santiago y San Jerónimo,
                las decisiones de localización mostraron un componente de
                proximidad familiar (n = 142, 61%)."
```

---

## Módulo 6 — Cohesión y estructura de párrafo

Ver `references/cohesion-parrafo.md` para la guía completa.

### 6a. Tamaño de párrafo `[LONG-PARR]`
- Por página: entre **3 y 8 párrafos**
- Por párrafo: **3–4 frases** como norma
- Párrafo-frase (1 sola oración): siempre incompleto, salvo efecto retórico deliberado
- Párrafo-lata (> 1 página): dividir en subunidades con oración tópica propia

### 6b. Oración tópica `[TOPICA]`
Todo párrafo abre con una afirmación que declara el argumento:

```
Sin oración tópica (débil):
"En 2023 se recolectaron 487 listings. La distribución mostró asimetría
 positiva (sk = 1.43). Aplicamos transformación logarítmica."

Con oración tópica (fuerte):
"Los datos de precios requirieron una transformación logarítmica antes del
 modelado. Se recolectaron 487 listings en 2023; la distribución exhibió
 asimetría positiva (sk = 1.43), lo que justificó aplicar ln(precio)."
```

### 6c. Marcadores textuales `[MARCADOR]`

| Función | Marcadores |
|---------|-----------|
| Orden / secuencia | en primer lugar, a continuación, finalmente, por último |
| Contraste | sin embargo, no obstante, por el contrario, ahora bien, aunque |
| Causa-efecto | por consiguiente, en consecuencia, por lo tanto, de ahí que |
| Énfasis / aclaración | es decir, esto es, en otras palabras, hay que destacar que |
| Adición | además, asimismo, del mismo modo, igualmente |
| Concesión | si bien, a pesar de que, aun cuando |

### 6d. Claridad de anáforas `[ANAFORA]`
Si el pronombre está a más de 2 frases de su referente, repetir el sustantivo:

```
Ambiguo: "El modelo SAR y el modelo SEM se estimaron con spreg. Él mostró
          mejor ajuste."
Claro:   "El modelo SAR y el modelo SEM se estimaron con spreg. El SAR
          mostró mejor ajuste (AIC = 1243 vs. 1389)."
```

---

## Módulo 7 — Checklist rápido (triage en 90 segundos)

Ante cualquier fragmento, revisar en este orden:

1. **¿Hay nominalizaciones acumuladas?** ("la realización de la evaluación de") → verbalizar
2. **¿Hay verbos comodín?** (hacer, tener, poner, dar) → sustituir por verbo preciso
3. **¿Hay muletillas?** ("a nivel de", "en base a", "de cara a") → eliminar
4. **¿Gerundio de posterioridad?** (acción mucho después del verbo principal) → subordinada relativa
5. **¿Gerundio adjetivo de cosa?** ("una ley regulando") → "una ley que regula"
6. **¿Dequeísmo?** ("pienso de que") → eliminar *de*
7. **¿Quien para cosas?** ("el modelo, quien…") → "el modelo, que…"
8. **¿Falta cuyo?** ("que su…") → cuyo/cuya/cuyos/cuyas
9. **¿Pleonasmo?** ("prever con antelación") → eliminar el elemento redundante
10. **¿Oración > 30 palabras?** → dividir
11. **¿Sujeto y verbo en las primeras 4 palabras?** → si no, reordenar (focalización)
12. **¿Circunstancial largo al inicio?** → mover al final
13. **¿Inciso largo (>15 palabras) o entre sujeto y verbo?** → mover al final / oración aparte
14. **¿Más de 2 niveles de subordinación o relativas encadenadas?** → fracturar
15. **¿Más de un adverbio en -mente cercano?** → reformular con sustantivo
16. **¿Párrafo sin oración tópica o fuera de 4–7 líneas?** → ajustar
17. **¿Anglicismo / falso amigo?** (reportar, evidencia, soportar, controlar por) → traducir
18. **¿Anacoluto?** (se abre "aunque…" y se cierra con "pero…") → completar la correlación
19. **¿El rema encadena con el tema siguiente?** → asegurar progresión temática
20. **¿Lenguaje sexista o reduccionista?** → persona-first + lenguaje neutro (sin /, @, -e)

---

## Módulo 8 — Salida del diagnóstico

```
## Diagnóstico de estilo académico en español

**Texto original:**
[reproducir el fragmento]

**Triage (checklist de 13 puntos):**
✓/✗ 1. Nominalizaciones
✓/✗ 2. Verbos comodín
...

**Errores detectados:**
[ETIQUETA] oración N: descripción del error
Regla: [nombre de la regla con referencia si aplica]

**Texto revisado:**
[versión corregida — misma información, prosa más ágil]

**Cambios realizados:**
1. [ETIQUETA] "texto original" → "texto corregido"
   Regla: [descripción de la regla]
```

Si el texto pasa todos los módulos: declarar
`✓ Prosa sin observaciones — el fragmento cumple los estándares de claridad,
economía léxica y corrección gramatical del español académico.`
y señalar los dos o tres puntos más fuertes del texto.

---

**Integración con scholarly-writing-coach:**
Este skill puede activarse de forma autónoma o tras el diagnóstico APA 7.
Cuando se use en combinación, el `scholarly-writing-coach` cubre los módulos
APA (voz, tiempos verbales, estadísticos, mecánica) y este skill cubre los
vicios propios del castellano (gramática española, léxico, cohesión de párrafo).

Cargar: `references/sintaxis-frase.md`, `references/vicios-gramaticales.md`,
`references/economia-lexica.md`, `references/lenguaje-inclusivo.md`,
`references/cohesion-parrafo.md`, `references/banco-casos.md`
