# Banco de casos resueltos — econometría espacial y urbanismo

Casebook aplicado con transformaciones antes/después, calibrado al dominio de
la tesis (mercado inmobiliario de Cusco, modelos hedónicos espaciales). Cada
caso integra las dos capas: corrección del castellano académico y rigor APA 7.

Usar estos casos como plantilla: identificar el patrón defectuoso en el texto
del usuario y aplicar la misma transformación.

---

## Caso 1 — Frases-cascada y subordinadas encadenadas

**Patrón:** un solo enunciado de 50+ palabras con gerundio inicial, relativas
anidadas y nominalización de cierre.

❌ **Defectuoso (53 palabras):**
> Habiéndose determinado que la topografía abrupta del Cusco Metropolitano, la
> cual influye en la accesibilidad vial a través de las pendientes pronunciadas,
> genera una distorsión espacial que afecta negativamente los precios
> inmobiliarios, se procedió a la aplicación de un modelo GWR con el objeto de
> evaluar el impacto real en el área de estudio.

✔️ **Corregido (3 unidades, orden S+V+C):**
> La topografía abrupta del Cusco Metropolitano restringe la accesibilidad vial
> debido a las pendientes pronunciadas. Esta distorsión física reduce los precios
> inmobiliarios. Para evaluar este impacto, aplicamos un modelo de regresión
> geográficamente ponderada (GWR).

**Qué se corrigió:** gerundio de apertura (`habiéndose determinado`) → fracturado;
relativa anidada (`la cual… que…`) → frases independientes; nominalización
(`se procedió a la aplicación`) → verbo activo (`aplicamos`).

---

## Caso 2 — Hipérbaton y circunstanciales antepuestos

**Patrón:** complementos circunstanciales al inicio que posponen el sujeto y el
verbo; adverbio dramático intercalado.

❌ **Defectuoso (29 palabras, sujeto bloqueado):**
> En las zonas periféricas del Cusco Metropolitano, debido a la falta de
> infraestructura vial y la presencia de topografía restrictiva, de manera muy
> drástica el precio del suelo urbano cae.

✔️ **Corregido (focalización inicial, 25 palabras):**
> El precio del suelo urbano cae en la periferia del Cusco Metropolitano. Esta
> contracción responde a la topografía restrictiva y a la carencia de
> infraestructura vial.

**Qué se corrigió:** circunstanciales movidos al final; núcleo (`el precio… cae`)
en las primeras palabras; eliminado el intensificador valorativo (`muy drástica`).

---

## Caso 3 — Verbos débiles y palabras comodín

**Patrón:** apilamiento de comodines (factor, tema, característica, cuestión) y
verbos vacíos (tener en cuenta, tener, producir).

❌ **Defectuoso (34 palabras):**
> Un factor a tener en cuenta en el tema del análisis es que la variable seguridad
> de la tenencia tiene una característica que produce un efecto positivo en la
> cuestión del valor de la tierra.

✔️ **Corregido (13 palabras):**
> La seguridad de la tenencia de la propiedad eleva el valor de la tierra.

**Qué se corrigió:** eliminados los 4 comodines; un único verbo predicativo de
alta densidad semántica (`eleva`); galicismo `a tener en cuenta` suprimido.

---

## Caso 4 — Párrafos fragmentados (oraciones huérfanas)

**Patrón:** tres frases de una línea, aisladas, sin marcadores ni oración tópica
que las articule.

❌ **Defectuoso (3 frases huérfanas):**
> Los modelos de mínimos cuadrados ordinarios (OLS) asumen homogeneidad espacial.
> Los residuos de estos modelos globales suelen presentar autocorrelación.
> Esto invalida los supuestos clásicos de la econometría.

✔️ **Corregido (bloque homogéneo, 4 líneas, 52 palabras):**
> Los modelos de mínimos cuadrados ordinarios (OLS) asumen homogeneidad espacial.
> Sin embargo, en el mercado inmobiliario del Cusco, los residuos de estas
> estimaciones globales suelen presentar autocorrelación espacial. Este fenómeno
> infringe los supuestos clásicos de la econometría y sesga los coeficientes
> estructurales. En consecuencia, es indispensable transicionar hacia algoritmos
> de regresión local.

**Qué se corrigió:** fusión en una unidad temática; marcadores explícitos al
inicio de oración (`Sin embargo`, `En consecuencia`); contextualización al caso
de estudio (`en el mercado inmobiliario del Cusco`).

---

## Caso 5 — Antropomorfismo en la descripción metodológica (capa APA 7)

**Patrón:** el modelo "se encarga de analizar", la sección "intenta demostrar",
y adjetivación literaria (`rol destructivo`).

❌ **Defectuoso (31 palabras):**
> El modelo SAR/SEM se encargó de analizar los errores espaciales, mientras que
> la sección de resultados intentó demostrar cómo las variables morfológicas
> juegan un rol destructivo en el valor del suelo periférico.

✔️ **Corregido (27 palabras, voz activa institucional):**
> Utilizamos el modelo SAR/SEM para analizar la estructura de los errores
> espaciales. Los resultados demuestran que las variables morfológicas de la
> periferia urbana reducen el precio del suelo.

**Qué se corrigió:** la acción cognitiva (`analizar`) reasignada al investigador
(`utilizamos… para analizar`); la sección no "intenta demostrar" → los resultados
`demuestran`; eliminada la adjetivación valorativa (`rol destructivo` → `reducen`).

---

## Cómo aplicar el banco

1. **Medir la oración:** ¿supera 30 palabras? → buscar el patrón del Caso 1 o 2.
2. **Contar comodines:** ¿hay factor / tema / aspecto / cuestión / característica? → Caso 3.
3. **Mirar el párrafo:** ¿hay frases de una línea sueltas? → Caso 4.
4. **Revisar la agencia:** ¿un modelo o una sección "hace" algo cognitivo? → Caso 5.
5. Aplicar la transformación y verificar que la información cuantitativa se conserva intacta.
