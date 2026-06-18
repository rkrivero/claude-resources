# Vicios gramaticales del español académico

## El gerundio — tipología de errores

### Usos CORRECTOS del gerundio
1. Acción anterior al verbo principal:
   `"Revisando los datos, detectamos tres valores atípicos."`
2. Acción simultánea:
   `"El modelo converge reduciendo iterativamente el error."`
3. Consecuencia inmediata y directa:
   `"El edificio colapsó, provocando el desalojo inmediato."`

### Gerundio de posterioridad `[GERU-POST]` — INCORRECTO
Acción claramente posterior o diferida en el tiempo:

| Incorrecto | Correcto |
|------------|---------|
| El agresor huyó, siendo detenido horas después. | El agresor huyó y fue detenido horas después. |
| Se produjo un accidente, muriendo todos. | Se produjo un accidente en el que murieron todos. |
| El precio subió, generando especulación en los meses posteriores. | El precio subió y generó especulación en los meses posteriores. |
| Se publicó la ley, entrando en vigor en enero. | Se publicó la ley, que entró en vigor en enero. |

### Gerundio adjetivo de cosa inanimada `[GERU-ADJ]` — INCORRECTO
El gerundio no puede funcionar como adjetivo calificativo de un sustantivo inanimado:

| Incorrecto | Correcto |
|------------|---------|
| una ley regulando los precios | una ley que regula los precios |
| un modelo conteniendo 12 variables | un modelo con 12 variables / que contiene 12 variables |
| datos mostrando asimetría positiva | datos que muestran asimetría positiva |
| tabla recogiendo los resultados | tabla que recoge los resultados |

**Galicismo "estar siendo + participio"** — preferir la construcción española:
| Galicismo | Español |
|-----------|---------|
| La oferta está siendo discutida. | La oferta se está discutiendo. |
| El modelo está siendo aplicado. | Se está aplicando el modelo. |

### Condicional "de + infinitivo/gerundio" → "si" `[DE-CONDICIONAL]`
Evitar la estructura "de + infinitivo (o gerundio)" con valor condicional al
inicio de la frase. Reemplazar por la conjunción *si*:

| Condicional con "de" | Con "si" |
|----------------------|---------|
| De aplicar el modelo SAR, los coeficientes cambiarían. | Si se aplica el modelo SAR, los coeficientes cambian. |
| De haberse incluido la variable, el R² subiría. | Si se hubiera incluido la variable, el R² subiría. |
| De no existir autocorrelación, el OLS bastaría. | Si no existiera autocorrelación, el OLS bastaría. |

---

## Galicismo "a + infinitivo" `[GALIC-A-INF]`

En español, los sintagmas nominales con función de complemento no llevan
la construcción francesa "sustantivo + a + infinitivo":

| Galicismo | Español correcto |
|-----------|-----------------|
| problemas a resolver | problemas que resolver / por resolver |
| ejemplos a seguir | ejemplos que seguir |
| tareas a realizar | tareas para realizar / que realizar |
| variables a controlar | variables que controlar / por controlar |
| temas a tratar | temas que tratar / pendientes de tratar |

---

## Dequeísmo y queísmo `[DEQUEISMO]` / `[QUEISMO]`

### Test de dequeísmo
Sustituye la cláusula *que…* por *eso*. Si la oración es correcta SIN *de*, hay dequeísmo:
- "Pienso **de** que es correcto" → "Pienso **de** eso" ✗ → eliminar *de*
- "Me alegra **de** que hayan llegado" → "Me alegra **de** eso" ✗ → eliminar *de*

Verbos que NUNCA llevan *de que*: pensar, creer, decir, saber, opinar, afirmar,
concluir, proponer, demostrar, indicar, sugerir.

### Queísmo (omitir *de* cuando sí debe ir) `[QUEISMO]`
Verbos y expresiones que SÍ van seguidos de *de que*:
- estar seguro **de que**, acordarse **de que**, darse cuenta **de que**,
  alegrarse **de que**, arrepentirse **de que**

| Queísmo | Correcto |
|---------|---------|
| Me di cuenta que había un error. | Me di cuenta **de que** había un error. |
| Estoy seguro que los datos son válidos. | Estoy seguro **de que** los datos son válidos. |

---

## Pronombres relativos

### Quien/quienes — solo para personas `[QUIEN-COSA]`

| Incorrecto | Correcto |
|------------|---------|
| El comité, quien decidirá… | El comité, **que** decidirá… |
| La ciudad a quien viajamos | La ciudad **a la que** viajamos |
| El informe, quien fue publicado… | El informe, **que** fue publicado… |

### Cuyo — pronombre relativo posesivo `[CUYO]`
"Que su" NO existe en español formal. Usar *cuyo/cuya/cuyos/cuyas*:

| Incorrecto | Correcto |
|------------|---------|
| un modelo que su R² supera el .50 | un modelo cuyo R² supera el .50 |
| el barrio que su densidad es alta | el barrio cuya densidad es alta |
| las variables que su significancia es baja | las variables cuya significancia es baja |

**Concordancia de "cuyo":** concuerda con el objeto **poseído**, nunca con el
poseedor: "el modelo, cuyas variables…" (cuyas → variables, no → modelo).
No usar "cuyo" como relativo simple con valor de *el cual* / *el que*:
```
Mal:  "Se estimó el SAR, cuyo mostró mejor ajuste."
Bien: "Se estimó el SAR, que mostró mejor ajuste."
```

### Restricción del relativo "que" `[REL-QUE]`
Máximo **dos** pronombres relativos por párrafo. La acumulación de subordinadas
con "que" se resuelve con adjetivos, participios o punto y seguido:
```
Recargado: "El modelo que se estimó, que incluye variables que capturan la
            accesibilidad, mostró el ajuste que esperábamos."
Resuelto:  "El modelo estimado incluye variables de accesibilidad. Su ajuste
            coincidió con lo esperado."
```

---

## Leísmo, laísmo, loísmo `[LEISMO]` / `[LAISMO]` / `[LOISMO]`

**Regla general (castellano estándar):**
- `le/les` → complemento indirecto (CI): "Le di el informe" (a él/ella)
- `lo/los` → CD masculino: "Lo vi" (al modelo / al distrito)
- `la/las` → CD femenino: "La analicé" (la variable)

| Error | Tipo | Correcto |
|-------|------|---------|
| La pegó un tortazo (CI) | Laísmo | **Le** pegó un tortazo |
| Los tomaron el pelo (CI) | Loísmo | **Les** tomaron el pelo |
| Le vi en el congreso (CD masculino, persona) | Leísmo (a veces aceptado) | **Lo** vi en el congreso |

**Nota:** El leísmo referido a persona masculina singular (`Le vi`) es aceptado
por la RAE en España pero no en América Latina. En texto académico peruano:
preferir **lo** para CD.

---

## "Deber" vs. "deber de" `[DEBER-DE]`

| Perífrasis | Significado | Ejemplo |
|------------|-------------|---------|
| deber + infinitivo | Obligación | Los datos **deben** verificarse antes del análisis. |
| deber de + infinitivo | Suposición / conjetura | Los precios **deben de** haber subido este trimestre. |

Error frecuente: usar "deber de" para obligación.
```
Mal:  "Los investigadores deben de declarar los conflictos de interés."
Bien: "Los investigadores deben declarar los conflictos de interés."
```

---

## Concordancia — casos especiales

### Sustantivos colectivos
Los colectivos en singular exigen verbo en singular:
- "El comité **tomó** la decisión." (no "tomaron")
- "La muestra **incluye** 487 predios." (no "incluyen")
- "El equipo de investigación **analizó**…" (no "analizaron")

**Sujeto colectivo cuantificador + complemento plural:** con "un porcentaje de
los datos", "la mayoría de los modelos", "un conjunto de variables", fijar el
verbo en **singular** por rigor formal (concuerda con el núcleo cuantificador):
- "Un porcentaje de los predios **carece** de título saneado." (no "carecen")
- "La mayoría de los modelos **converge** en menos de 50 iteraciones." (no "convergen")
- "Un grupo de investigadores **analizó** los datos." (formal singular)

### "Uno de los que…" — verbo en plural
```
Correcto: "Fue uno de los que lo protegieron." (no "protegió")
Correcto: "Yo soy de los que creen que…" (no "creo que")
```

### Sujetos compuestos con "o/ni"
El verbo concuerda con el elemento más próximo:
```
"Ni el modelo SAR ni el modelo SEM captura toda la dependencia espacial."
↑ concuerda con "el modelo SEM"
```

---

### Adjetivo que califica a sustantivos coordinados
Un solo adjetivo para dos o más sustantivos de distinto género/número:

- **Pospuesto** → plural y masculino: "el precio y la superficie *registrados*"
- **Antepuesto** → concuerda solo con el más próximo: "los *escasos* registros y
  observaciones" (concuerda con "registros")

```
Pospuesto:  "Se depuraron los predios y las transacciones duplicados."
Antepuesto: "Se analizaron las distintas variables y atributos."
```

---

## Anacolutos — ruptura de la correlación sintáctica `[ANACOLUTO]`

No abandonar a mitad de frase una estructura ya abierta (condicional, concesiva,
distributiva): completar la secuencia manteniendo la concordancia del sujeto.

```
Anacoluto: "Aunque el modelo OLS es simple, pero sus residuos violan los supuestos."
           (se abre "aunque" y se cierra con "pero" — correlación rota)
Correcto:  "Aunque el modelo OLS es simple, sus residuos violan los supuestos."

Anacoluto: "El investigador que recolecta datos propios, su análisis es más sólido."
           (cambia de sujeto a mitad de frase)
Correcto:  "El análisis del investigador que recolecta datos propios es más sólido."
```

---

## Infinitivo como imperativo `[INF-IMPER]`
Error frecuente en instrucciones y metodologías escritas:

| Incorrecto | Correcto |
|------------|---------|
| ¡Callar! | ¡Cállense! / ¡Callaos! |
| No fumar en el área de estudio | No se fuma en el área / Queda prohibido fumar |
| Rellenar el formulario con… | Rellene el formulario con… / Se ruega rellenar… |

---

## Preposición "a" redundante

### "A por" — evitar en texto formal
```
Coloquial:  "Voy a por los datos."
Formal:     "Voy por los datos."
```

### "A" antes de adverbios que la llevan implícita
```
Incorrecto: "ven a acá", "sube a arriba"
Correcto:   "ven acá", "sube"
```

---

## Anglicismos sintácticos y falsos amigos `[ANGLICISMO]`

### Falsos amigos del inglés científico

| Calco | Español correcto |
|-------|------------------|
| evidencia (de *evidence*) | pruebas, hallazgos, datos, resultados (reservar "evidencia" para lo palmario/obvio) |
| reportar (de *to report*) | informar, registrar, describir, detallar, presentar |
| controlar por (de *to control for*) | controlar en función de, aislar el efecto de |
| soportar (de *to support*) | sustentar, respaldar, validar, apoyar |
| consistente con (a veces) | coherente con, compatible con |
| asumir (de *to assume*) | suponer, presuponer, dar por sentado |
| sensible (de *sensitive*) | delicado, considerable (según contexto) |
| dramático (de *dramatic*) | acusado, marcado, pronunciado |

```
Calco:    "Los resultados soportan la hipótesis y reportan evidencia de que la
           accesibilidad controla por el precio."
Correcto: "Los resultados respaldan la hipótesis y aportan pruebas de que la
           accesibilidad, controlada en función del precio, …"
```

### Pasiva analítica de influencia anglosajona
Evitar "Sujeto + ser + participio + por + agente" para procedimientos técnicos.
Usar pasiva refleja (*se*) o primera persona plural:

```
Anglicada: "Los datos fueron recolectados por los autores."
Refleja:   "Los datos se recolectaron a partir de portales inmobiliarios."
Activa:    "Recolectamos los datos de portales inmobiliarios."
```

### Adjetivación antepuesta (calco)
Mantener la posposición natural del adjetivo especificativo:

```
Calco:    "una significativa correlación espacial"
Español:  "una correlación espacial significativa"
```

### "Ser capaz de" aplicado a modelos `[CAPAZ-DE]`
Las herramientas no poseen capacidades humanas: ejecutan, calculan, permiten.

```
Mal:  "El modelo GWR es capaz de capturar la heterogeneidad espacial."
Bien: "El modelo GWR capta la heterogeneidad espacial."
Bien: "El modelo GWR permite estimar coeficientes locales."
```

---

## Puntuación — errores graves

### Dos puntos después de "que" `[DOSP-QUE]`
```
Incorrecto: "Dijo que: 'Sería conveniente ampliar la muestra.'"
Correcto 1: "Dijo: 'Sería conveniente ampliar la muestra.'"
Correcto 2: "Dijo que sería conveniente ampliar la muestra."
```

### Tres funciones de los dos puntos `[DOSP-FUNCION]`
Restringir los dos puntos a estos tres usos metodológicos:

1. **Anunciar una enumeración previamente anticipada:**
   `"Se consideraron tres categorías de atributos: estructurales, de vecindad y de accesibilidad."`
2. **Conectar una afirmación general con su demostración:**
   `"El modelo confirma la hipótesis: la distancia al CHC reduce el precio (β = −.38, p < .001)."`
3. **Ligar causa y consecuencia sin conector verbal:**
   `"Los residuos violan la independencia: se impone un modelo espacial."`

### Anfibología — ambigüedad referencial `[ANFIBOLOGIA]`
Disponer pronombres relativos, posesivos y complementos en contigüidad con el
sustantivo exacto que modifican. Si hay dos antecedentes posibles, reordenar:

```
Ambiguo:  "Se analizó el precio del suelo de los distritos que presentan
           mayor pendiente." → ¿el precio o los distritos presentan pendiente?
Claro:    "Se analizó el precio del suelo en los distritos de mayor pendiente."

Ambiguo:  "García citó a Rosen en su artículo." → ¿el artículo de quién?
Claro:    "En su artículo, García citó a Rosen." / "García citó el artículo de Rosen."
```

### Punto y coma antes de conectores adversativos/consecutivos
Usar `;` (no coma) cuando las oraciones son extensas:
```
Ambiguo con coma: "El modelo OLS fue significativo, sin embargo los residuos
                   mostraron dependencia espacial."
Con punto y coma: "El modelo OLS fue significativo; sin embargo, los residuos
                   mostraron dependencia espacial."
```

### Punto y seguido como articulador primario `[PUNTO-SEGUIDO]`
El punto y seguido es el mecanismo primario de división de ideas independientes
que comparten el mismo eje temático. Debe prevalecer sobre la coma en el diseño
general del texto: ante la duda entre coma y punto, elegir punto.

```
Sobrecargada con comas:
"La distancia al CHC fue el predictor principal, su coeficiente fue negativo,
 lo que indica que el precio cae al alejarse, este patrón es consistente con
 la teoría de capitalización patrimonial."

Segmentada con puntos:
"La distancia al CHC fue el predictor principal. Su coeficiente negativo indica
 que el precio cae al alejarse. Este patrón concuerda con la teoría de
 capitalización patrimonial."
```

### Aislamiento bilateral de conectores `[COMA-NEXO]`
Las locuciones de transición y los nexos parentéticos intercalados van entre
comas a ambos lados:

```
Mal:  "Los residuos por lo tanto mostraron dependencia espacial."
Bien: "Los residuos, por lo tanto, mostraron dependencia espacial."

Mal:  "El ajuste fue bueno y sin embargo persiste heterocedasticidad."
Bien: "El ajuste fue bueno y, sin embargo, persiste heterocedasticidad."
```

### Coma tras estructura antepuesta `[COMA-ANTEPUESTA]`
Toda cláusula circunstancial o subordinada adverbial antepuesta al sujeto que
supere las **5 palabras** lleva coma de cierre:

```
"Dado que los residuos violan el supuesto de independencia, se estimó SAR."
"Tras verificar la calidad del CRS, se construyó la matriz de pesos."
```

Si la estructura antepuesta es corta (≤5 palabras), la coma es opcional:
`"En 2023 se recolectaron 487 listings."` (sin coma)

### Inviolabilidad sujeto-verbo `[COMA-SV]`
Nunca una coma aislada entre el sujeto y su verbo, por largo que sea el sujeto:
```
Mal:  "Los predios del Centro Histórico del Cusco declarado Patrimonio, superan
       el promedio."
Bien: "Los predios del Centro Histórico del Cusco, declarado Patrimonio,
       superan el promedio." (las dos comas aíslan el inciso, no separan S-V)
Bien: "Los predios del Centro Histórico superan el promedio." (sin inciso)
```
