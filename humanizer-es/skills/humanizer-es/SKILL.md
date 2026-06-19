---
name: humanizer-es
description: >
  Elimina los rastros de español generado por IA para que el texto no se lea
  como salida de chatbot. Disparadores: "suena a IA", "suena a ChatGPT", "huele
  a generado", "quítale lo robótico", "humaniza este texto", "esto lo escribió
  una IA", "el abstract suena artificial", "tiene muchos 'cabe destacar'",
  "quita los tripletes", "suena a traducción del inglés con IA", "hazlo más
  humano". Detecta tells del LLM en castellano: inflación de significancia,
  lenguaje promocional, vocabulario-IA ("cabe destacar", "juega un rol crucial",
  "en el marco de"), evitación de ser/estar, atribuciones vagas, paralelismos
  negativos, regla de tres, falsos rangos, señalización, conclusiones-globo,
  adulación, hedging, artefactos de chatbot y mecánica (raya anglosajona,
  comillas, emojis, mayúsculas en títulos). Incluye también vicios gramaticales
  del castellano (gerundios, dequeísmo, nominalización), que comparte con
  `corrector-prosa-castellana`. Complementa `legibilidad-estilo-llano` y
  `diagnostico-textual`.
metadata:
  version: "0.3.0"
  author: "academic-spanish-style"
  source: "Adaptación al castellano de Wikipedia:Signs of AI writing (WikiProject AI Cleanup) + observación de salida de LLM en español"
  changelog: "0.3.0 — añade tells §30-40 (gerundio analítico, sección formularia, variación elegante, contraste retroactivo, encabezados fragmentados, drama staccato, aforismos, aperturas conversacionales, variedad es-ES/es-419, plantillas sin editar, escritura anclada al cambio); afina §21 (aditivos vs concesivos); anti-fabricación adaptada al registro. 0.2.0 — vicios gramaticales del castellano (§22-29)."
---

# Humanizer-ES — Quitar los tells de IA en español

Identifica y elimina las señales de texto generado por IA en castellano para que
la prosa suene a persona. Es la contraparte hispana del skill `humanizer`
(inglés), calibrada a los tells que producen ChatGPT/Claude/Gemini al escribir
en español, no una traducción literal.

No pedir permiso antes de reescribir. No omitir tells por cortesía.

## Tarea

1. **Detectar** los patrones de abajo (clusters, no señales aisladas).
2. **Reescribir, no borrar.** Cubrir todo lo que cubría el original; mismo número
   de párrafos e ideas.
3. **Preservar el significado** y los datos (cifras, citas, resultados).
4. **Respetar el registro** según el interruptor de abajo.

El loop borrador → ¿qué lo delata? → final y el entregable están al final, en
**Proceso y salida**.

---

## Interruptor de registro (decidir SIEMPRE primero)

El tratamiento cambia según el tipo de texto:

- **Académico** (tesis, abstract, paper, informe técnico): neutro, llano y
  preciso *es* la voz humana correcta. **No** inyectar opinión, primera persona
  ni "personalidad". Quitar tells y devolver prosa sobria. Conservar terminología
  técnica y cifras.
- **No-académico** (divulgación, blog, post, correo, texto personal): además de
  quitar tells, **recuperar voz**: ritmo variado, opinión real, detalle concreto,
  primera persona cuando corresponda. Aquí la prosa estéril también es un tell.

Si el registro no es obvio, dedúcelo del texto; si sigue ambiguo, pregúntalo en
una línea antes de reescribir.

## Calibración de voz (opcional)

Si el usuario aporta una muestra de su escritura, analízala antes de reescribir
(longitud de frase, nivel léxico, cómo abre párrafos, puntuación, tics) y replica
ese patrón en vez del genérico. Sin muestra: en académico, sobrio; en no-académico,
voz variada y con opinión.

---

## TELLS DE CONTENIDO

### 1. Inflación de significancia y trascendencia `[IA-SIGNIF]`
La IA hincha la importancia de cualquier dato con frases de "papel" y "legado".

**Vigilar:** juega un papel/rol crucial·fundamental·clave, se erige/consolida/posiciona
como, constituye un hito·punto de inflexión, marca un antes y un después, sienta las
bases de, refleja una tendencia más amplia, deja una huella imborrable·indeleble,
testimonio·muestra de, en un mundo cada vez más.

```
Antes: "El catastro de 1989 marcó un hito en la evolución del análisis territorial
        del Cusco y sentó las bases de una nueva era en la planificación urbana."
Final: "El catastro de 1989 fue el primer registro sistemático de predios del Cusco."
```

### 2. Lenguaje promocional / publicitario `[IA-PROMO]`
Tono de folleto turístico, sobre todo en temas "culturales" o de lugar.

**Vigilar:** rica·vibrante·fascinante tradición, enclavado·ubicado en el corazón de,
impresionante·deslumbrante, joya, no podemos dejar de mencionar, paisaje (figurado),
crisol·mosaico·tapiz·entramado de, robusto (calco de *robust*).

```
Antes: "Enclavado en el corazón de los Andes, el vibrante distrito de San Jerónimo
        es una joya de rica tradición y paisaje deslumbrante."
Final: "San Jerónimo es un distrito del sureste de Cusco; concentra mercados
        mayoristas y suelo agrícola en proceso de urbanización."
```

### 3. Vocabulario-IA de alta frecuencia `[IA-LEXICO]`
Palabras y arranques que la IA usa muchísimo más que la prosa humana (tabla
completa en `references/tells-ia-castellano.md`).

**Muestra:** cabe destacar·mencionar·resaltar, es importante señalar, en el marco
de·en el ámbito de, abordar (por *tratar*), profundizar·ahondar en, de la mano de,
a su vez, por ende, así pues, en la actualidad·hoy en día (como muletilla de
arranque), sin duda, en constante evolución, enriquecedor.

```
Antes: "Cabe destacar que, en el marco de la planificación, la accesibilidad juega
        un papel a su vez fundamental."
Final: "La accesibilidad es uno de los factores más influyentes en el precio."
```

### 4. Evitación de "ser/estar" — cópula `[IA-COPULA]`
La IA sustituye el verbo *ser/estar* por verbos de relleno.

**Vigilar:** constituye, representa, supone, conforma, se configura como, viene a ser,
funge como, opera como.

```
Antes: "El precio del suelo constituye un indicador que representa la estructura urbana."
Final: "El precio del suelo es un indicador de la estructura urbana."
```

### 5. Atribuciones vagas y comodines de autoridad `[IA-VAGO]`
Opiniones colgadas de autoridades sin nombre ni cita.

**Vigilar:** diversos·numerosos expertos señalan, según diversas fuentes, está
ampliamente aceptado que, la literatura coincide en (sin referencia), se considera·cree
que, es bien sabido que.

```
Antes: "Numerosos expertos coinciden en que el modelo es ampliamente robusto."
Final: "El modelo MGWR mejoró el AIC frente al OLS (1243 vs. 1389)." (o cita la fuente)
```

### 6. Paralelismos negativos `[IA-PARALELO]`
Construcciones contrastivas sobreusadas.

**Vigilar:** no solo… sino (que) también, no se trata de… sino de, más que X, es Y,
no es (solo) … es ….

```
Antes: "No se trata solo de medir precios, sino de comprender la ciudad; no es un
        dato, es una declaración."
Final: "Medir los precios ayuda a entender cómo se estructura la ciudad."
```

### 7. Regla de tres / tripletes `[IA-TRIPLETE]`
Ideas forzadas en grupos de tres para sonar completo.

```
Antes: "El estudio aporta innovación, rigor y sostenibilidad al análisis urbano."
Final: "El estudio aplica econometría espacial a un problema poco estudiado en Cusco."
```

### 8. Falsos rangos `[IA-RANGO]`
"Desde X hasta Y" donde X e Y no están en una misma escala.

```
Antes: "El precio depende de todo, desde la accesibilidad hasta la identidad del barrio."
Final: "El precio depende de la accesibilidad, los servicios y la zonificación."
```

### 9. Señalización y anuncios `[IA-SEÑAL]`
La IA anuncia lo que va a hacer en vez de hacerlo.

**Vigilar:** profundicemos·exploremos·analicemos, en este artículo·apartado veremos·
exploraremos, a lo largo de este texto, sin más preámbulo, vamos a desglosar.

```
Antes: "En este apartado, exploraremos cómo funciona la autocorrelación espacial.
        Profundicemos."
Final: "La autocorrelación espacial implica que valores cercanos tienden a parecerse."
```

### 10. Conclusiones-globo / cierres genéricos `[IA-CIERRE]`
Finales optimistas y vacíos.

**Vigilar:** el futuro es prometedor, un futuro brillante, tiempos emocionantes,
solo el tiempo lo dirá, en definitiva queda mucho por recorrer, un paso en la
dirección correcta.

```
Antes: "En conclusión, el futuro de la planificación es prometedor y nos esperan
        tiempos emocionantes."
Final: "El siguiente paso es validar el modelo con los datos catastrales de 2024."
```

### 11. Tropos de autoridad persuasiva `[IA-AUTORIDAD]`
Frases que fingen ir "al fondo" antes de repetir algo común.

**Vigilar:** la verdadera pregunta es, en esencia, en el fondo, lo que realmente
importa, a fin de cuentas, en última instancia.

```
Antes: "La verdadera pregunta es si el modelo sirve. En esencia, lo que importa es el ajuste."
Final: "La cuestión es si el modelo predice bien fuera de la muestra."
```

### 12. Artefactos de chatbot `[IA-CHATBOT]`
Texto de conversación pegado como contenido.

**Vigilar:** ¡Claro!, ¡Por supuesto!, ¡Excelente pregunta!, aquí tienes, a
continuación te presento, espero que esto (te) ayude·sirva, ¿te gustaría que…?,
¿quieres que…?, si necesitas algo más, como modelo de lenguaje, hasta mi última
actualización.

```
Antes: "¡Claro! Aquí tienes un resumen del marco teórico. Espero que te sirva. ¿Quieres
        que lo amplíe?"
Final: "El marco teórico parte de la teoría de la renta del suelo de Alonso (1964)…"
```

### 13. Adulación / servilismo `[IA-ADULA]`
**Vigilar:** ¡Gran pregunta!, tienes toda la razón, excelente punto, como bien dices.
Eliminar la cortesía vacía y dejar el contenido.

### 14. Hedging excesivo y relleno `[IA-RELLENO]`
Sobre-matización y locuciones vacías (las muletillas gramaticales puras —"a nivel
de", "en base a"— se delegan a `corrector-prosa-castellana`).

**Vigilar:** podría potencialmente, quizás tal vez, es posible que eventualmente,
de alguna manera, en cierto sentido, valga la redundancia.

```
Antes: "Podría potencialmente quizás argumentarse que la política tal vez tenga
        algún efecto."
Final: "La política puede afectar los precios."
```

### 15. Disclaimers de corte y relleno especulativo `[IA-CORTE]`
**Vigilar:** hasta mi última actualización, si bien la información es limitada,
con base en la información disponible, no se dispone de datos públicos, mantiene un
perfil bajo, probablemente (rellenando un vacío). Decir qué no se sabe, o cortar la
frase; no disfrazar una conjetura de dato.

---

## TELLS DE MECÁNICA Y FORMATO (español)

> Aquí divergimos de `humanizer` (inglés). En español **la raya y las comillas
> latinas son legítimas**; no se prohíben. Se marca solo el uso anglosajón.

### 16. Raya (—) a la inglesa `[IA-RAYA]`
La raya es correcta en castellano para incisos —así, sin espacios interiores— y
para diálogo. El tell es el **guion largo con espacios al estilo inglés** (` — `)
usado como pausa dramática, y el **doble guion** (` -- `). Convertir el inciso a
raya española sin espacios, o a coma/paréntesis; eliminar la pausa dramática.

```
Antes: "El modelo — según los datos — falló. El catastro -- ya obsoleto -- se ignoró."
Final: "El modelo falló según los datos. El catastro, ya obsoleto, se ignoró."
```

### 17. Comillas `[IA-COMILLAS]`
La IA usa comillas curvas inglesas (“…”) por defecto. En texto académico en
español, preferir **«comillas latinas»**; en su defecto, "rectas". Reservar las
‘simples’ para entrecomillar dentro de otra cita.

### 18. Negritas mecánicas y listas "encabezado: …" `[IA-NEGRITA]`
Quitar negritas decorativas y deshacer las listas con encabezado en negrita +
dos puntos cuando el contenido fluye mejor en prosa.

### 19. Mayúsculas en títulos `[IA-TITULAR]`
El inglés usa *Title Case*. El español usa **mayúscula solo en la inicial** (y
nombres propios). `## Análisis De Los Precios Del Suelo` → `## Análisis de los
precios del suelo`.

### 20. Emojis `[IA-EMOJI]`
Quitar emojis decorativos en encabezados y viñetas (salvo que el registro
informal lo pida explícitamente).

### 21. Conectores aditivos apilados `[IA-CONECTOR]`
El tell fuerte es el **apilamiento de conectores aditivos/secuenciales** (además,
asimismo, igualmente, del mismo modo, por otro lado, en este sentido), no los
concesivos. La prosa humana usa *más* los concesivos (sin embargo, no obstante,
aunque); un concesivo aislado NO es tell. Marcar solo el apilamiento aditivo;
dejar uno o ninguno.

---

## TELLS GRAMATICALES DEL CASTELLANO (compartidos)

Vicios del castellano que la IA comete a menudo. Se marcan aquí —con las mismas
etiquetas que `corrector-prosa-castellana`— para que el skill sea autosuficiente.
Para el tratamiento exhaustivo (tablas completas, casos), encadenar con
`corrector-prosa-castellana`.

### 22. Nominalización excesiva `[NOMINAL]`
Sustantivos abstractos que desplazan al verbo.
```
Antes: "Se procedió a la realización de la validación de los datos."
Final: "Se validaron los datos."
```

### 23. Verbos comodín `[COMODIN]`
```
Antes: "Se hizo un análisis y se tuvo en cuenta la accesibilidad."
Final: "Se analizó la accesibilidad." / "Se consideró la accesibilidad."
```

### 24. Gerundio de posterioridad `[GERU-POST]`
El gerundio no expresa acción posterior al verbo principal.
```
Antes: "Se estimó el modelo, publicándose los resultados meses después."
Final: "Se estimó el modelo; los resultados se publicaron meses después."
```

### 25. Gerundio adjetivo de cosa `[GERU-ADJ]`
```
Antes: "Una ley regulando los precios." / "Un modelo conteniendo 12 variables."
Final: "Una ley que regula los precios." / "Un modelo con 12 variables."
```

### 26. Dequeísmo / queísmo `[DEQUEISMO]`
```
Antes: "Pienso de que el modelo es adecuado." (dequeísmo)
Final: "Pienso que el modelo es adecuado."
Antes: "Estoy seguro que mejora." (queísmo)
Final: "Estoy seguro de que mejora."
```
*Prueba:* sustituye la cláusula por *eso*. "Pienso eso" (sin *de*) → era dequeísmo.

### 27. Galicismo "a + infinitivo" `[GALIC-A-INF]`
```
Antes: "Las variables a controlar." 
Final: "Las variables que se deben controlar." / "Las variables por controlar."
```

### 28. "Quien" para cosas `[QUIEN-COSA]`
```
Antes: "El comité, quien decidirá…"  →  Final: "El comité, que decidirá…"
```

### 29. Falta de "cuyo" `[CUYO]`
```
Antes: "Un modelo que su R² supera .50."  →  Final: "Un modelo cuyo R² supera .50."
```

---

## TELLS ADICIONALES

### 30. Análisis superficial con gerundio al final `[IA-ANALISIS-ING]`
La IA cuelga un gerundio "analítico" al final de la frase para fingir profundidad
o colar una valoración. Es gerundio correcto (≠ §24/§25), pero el contenido es
relleno. **Vigilar:** …reflejando, …subrayando, …lo que refleja·demuestra·evidencia
un compromiso con, …contribuyendo así a, …consolidando su.
```
Antes: "El sistema admite varias billeteras, mejorando así la comodidad del usuario."
Final: "El sistema admite varias billeteras."
```

### 31. Sección formularia "Desafíos y perspectivas" `[IA-SECCION]`
Apartados-plantilla que el LLM añade por inercia. **Vigilar:** "A pesar de estos
desafíos…", "Desafíos y perspectivas futuras", "De cara al futuro", "Retos y
oportunidades". Sustituir por hechos concretos o eliminar.

### 32. Variación elegante (sinónimos en cadena) `[IA-SINONIMO]`
La IA evita repetir un sustantivo ciclando sinónimos.
```
Antes: "El protagonista enfrenta retos. El personaje principal los supera. La figura central triunfa."
Final: "El protagonista enfrenta retos, los supera y triunfa."
```
En académico, **repetir el término técnico exacto es correcto** (el modelo, el
modelo); no ciclar "el modelo / la herramienta / el instrumento / el dispositivo".

### 33. Contraste retroactivo / "corregir una idea errónea" `[IA-CONTRASTE]`
El LLM finge desmentir una creencia que nadie planteó. **Vigilar:** "Contrario a lo
que muchos piensan…", "Aunque suele creerse que…, en realidad…", "Lejos de ser X,
es Y".
```
Antes: "Contrario a la creencia popular, el precio del suelo no depende solo de la ubicación."
Final: "El precio del suelo depende de la ubicación, los servicios y la zonificación."
```

### 34. Encabezados fragmentados `[IA-ENCABEZADO]`
Un título seguido de una línea que solo lo repite antes del contenido real.
```
Antes:  ## Rendimiento / La velocidad importa. / Cuando una página tarda, el usuario se va.
Final:  ## Rendimiento / Cuando una página tarda, el usuario se va.
```

### 35. Drama staccato / frases-puñetazo `[IA-STACCATO]`
Ráfaga de frases cortas para fabricar dramatismo. Una frase corta para enfatizar
está bien; varias seguidas, no.
```
Antes: "Entonces llegó el modelo. Sin sesgos. Sin supuestos. Las viejas reglas cayeron."
Final: "El nuevo modelo no arrastraba los supuestos del anterior, lo que invalidó varias reglas previas."
```

### 36. Fórmulas de aforismo `[IA-AFORISMO]`
Convierte una idea común en frase "profunda". **Vigilar:** "X es el lenguaje de Y",
"X es la moneda de Y", "la arquitectura de", "X no es una herramienta, es un espejo".
```
Antes: "La simetría es el lenguaje de la confianza."
Final: "Las distribuciones simétricas suelen parecer más predecibles al lector."
```

### 37. Aperturas retóricas conversacionales `[IA-APERTURA]`  (sobre todo no-académico)
Gancho de falsa franqueza antes de decir algo común. **Vigilar:** "¿La verdad?",
"Mira,", "A ver,", "Seamos honestos,", "Te seré franco,", "Aquí va la cosa:".
```
Antes: "¿Vale la pena? ¿La verdad? Depende de cuánto lo uses."
Final: "Si vale la pena depende de cuánto lo uses."
```

### 38. Discrepancias de variedad del español `[IA-VARIEDAD]`
La IA mezcla variedades (peninsular vs americana) sin coherencia, o usa una que no
es la del autor. Para texto en **español de América (es-419)**, vigilar deslices
peninsulares y mantener una sola variedad:

| Peninsular (desliz IA) | América (es-419) |
|---|---|
| ordenador | computadora |
| coche | auto / carro |
| móvil | celular |
| zumo | jugo |
| vosotros tenéis | ustedes tienen |
| le vio (leísmo) | lo vio |
| "vale" (muletilla) | "de acuerdo" / "listo" |

### 39. Plantillas/placeholders sin editar `[IA-PLANTILLA]`
Restos de andamiaje del prompt: "[insertar cita]", "[autor, año]", "como se
mencionó en la sección X" (sin que exista), "a continuación se detalla:" sin
detalle. Eliminar o completar.

### 40. Escritura anclada al cambio `[IA-DIFF]`  (registro técnico/documentación)
Comentarios o docs que narran un cambio en vez de describir el estado. Salvo
changelogs o notas de versión, el texto debe leerse sin saber qué cambió.
```
Antes: "Esta función se agregó para reemplazar el bucle anterior, que era O(n²)."
Final: "Esta función usa un diccionario para búsquedas O(1)."
```

---

## DETECCIÓN

### Qué NO marcar (falsos positivos en español)
- **La raya (—) bien usada** para incisos o diálogo. Solo es tell la versión
  espaciada a la inglesa o la pausa dramática.
- **Las «comillas latinas».** Son la norma culta, no un defecto.
- **Vocabulario formal o técnico legítimo** (heterocedasticidad, autocorrelación,
  hedónico). La IA abusa de palabras *concretas* (§3), no de todo término culto.
- **Conectores concesivos aislados** (*sin embargo*, *no obstante*, *aunque*).
  Son *más* frecuentes en humanos; solo cuenta el apilamiento de **aditivos** (§21).
- **Pares con guion al estilo inglés** (data-driven, end-to-end): no se forman así
  en español; no es un tell aplicable aquí.
- **Voz pasiva o impersonal** propia del registro académico ("se estimó", "se
  observó"). Es norma del género, no un tell.
- **Repetir el término técnico exacto** (el modelo, el modelo). Es lo correcto;
  ciclar sinónimos (§32) es el tell, no la repetición.
- **Buena gramática y estilo pulido.** Pulcritud no es IA.

Buscar **clusters**: una raya aislada no dice nada; raya espaciada + triplete +
"cabe destacar" + cierre-globo es una confesión.

### Señales de escritura humana (preservar)
Detalle específico difícil de inventar, sentimientos encontrados sin resolver,
referencias datadas a un año o subcultura, asides y autocorrecciones entre
paréntesis, variedad real de longitud de frase, decisiones de autor que el autor
sabría defender. Ante la duda, no aplanar.

---

## Proceso y salida

1. Decide el **registro** (académico / no-académico).
2. Lee el texto e identifica cada tell de las secciones de arriba.
3. Escribe un **borrador** de reescritura. Comprueba que se lee bien en voz alta,
   varía la longitud de frase, prefiere *ser/estar/tener* y el dato concreto, y
   mantiene el registro.
4. Pregúntate: **"¿Qué delata todavía que esto lo escribió una IA?"** Responde en
   2-4 viñetas con los tells residuales.
5. Reescribe la **versión final** que los corrige.

**Entregable, en este orden:**
- **Borrador**
- **¿Qué lo delata todavía?** (viñetas)
- **Versión final**
- (opcional) **Cambios**: lista breve `[ETIQUETA] "antes" → "después"`.

Si el texto no tiene tells, decláralo y señala 2-3 cosas que ya suenan humanas.

**Regla anti-fabricación (innegociable, adaptada al registro).** Quitar tells nunca
autoriza a inventar. El umbral cambia según el tipo de texto:
- **Académico** (tesis, paper, informe): cero invención. No añadas cifras, fechas,
  autores, citas ni resultados ausentes. Si falta el dato, deja la frase vaga o
  marca `[falta dato concreto]`. (Coherente con tu norma de cero citas inventadas.)
- **Técnico / documentación / código:** no inventes comportamiento, banderas, rutas,
  nombres de función ni versiones. Si no consta, escribe "no documentado" o marca el
  hueco.
- **Divulgación / blog:** puedes recuperar voz y usar ejemplos ilustrativos
  *marcados como hipotéticos* ("imagina que…", "pongamos por caso…"), pero no datos
  factuales falsos (cifras, fechas, nombres o estudios reales) que el lector tomaría
  por ciertos.
- **Personal / correo:** no inventes hechos, compromisos ni detalles que el autor no
  haya dicho.

En la duda, marca el vacío; no lo rellenes.

Antes de entregar la versión final, escanéala: si encuentras léxico de §3, raya
espaciada (§16) o comillas curvas inglesas (§17), aún no está lista.

---

## Ejemplo (registro académico)

**Antes (suena a IA):**
> Cabe destacar que, en el marco de la planificación urbana contemporánea, el
> precio del suelo se erige como un indicador que no solo refleja el mercado, sino
> que constituye un testimonio de la estructura de la ciudad. En la actualidad,
> diversos expertos coinciden en que su estudio es fundamental. El presente trabajo
> aborda, de la mano de la econometría espacial, un análisis robusto que aporta
> rigor, innovación y profundidad. En definitiva, el futuro de esta línea de
> investigación es prometedor.

**¿Qué lo delata?**
- Léxico-IA en cascada: "cabe destacar", "en el marco de", "se erige", "aborda",
  "de la mano de", "en la actualidad".
- Paralelismo negativo ("no solo… sino que") + cópula evitada ("constituye un
  testimonio de").
- Triplete ("rigor, innovación y profundidad") y atribución vaga ("diversos
  expertos").
- Cierre-globo ("el futuro es prometedor").

**Versión final:**
> El precio del suelo es un indicador de la estructura urbana: dónde se concentra
> el valor y cómo se organiza la ciudad. Este trabajo lo analiza con econometría
> espacial (MGWR), un enfoque poco aplicado al caso de Cusco. El objetivo es medir
> qué factores explican el precio y a qué escala opera cada uno.

---

## Integración dentro del plugin

- `corrector-prosa-castellana` — vicios del castellano (gerundios, dequeísmo,
  nominalización, comodines). **Solapamiento intencional:** humanizer-es también
  los marca (§22-29) para ser autosuficiente, pero con tablas mínimas; para el
  tratamiento exhaustivo (casos, banco completo, sintaxis, cohesión) encadenar con
  `corrector-prosa-castellana`. Secuencia: humanizer-es primero, corrector después.
- `legibilidad-estilo-llano` — densidad y legibilidad; complementario cuando, además
  de sonar a IA, el texto es difícil de leer.
- `diagnostico-textual` — diagnóstico por capas (Cassany); úsalo cuando el problema
  es de coherencia/adecuación, no de tells de IA.

Para más casos antes/después en dominio cuantitativo-espacial y un caso de
divulgación, ver `references/banco-casos.md`. Para la tabla exhaustiva de
léxico/frases-IA en español y calcos del inglés, ver `references/tells-ia-castellano.md`.

## Fuente
Adaptación al castellano de [Wikipedia:Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)
(WikiProject AI Cleanup) y del skill `humanizer` (blader), recalibrada con la
salida real de LLM en español.
