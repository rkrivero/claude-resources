# Cohesión y estructura del párrafo académico

## Anatomía del párrafo académico

```
[1] ORACIÓN TÓPICA — la afirmación central del párrafo (15–25 palabras)
[2] DESARROLLO — evidencia, datos, análisis que sustentan la oración tópica
[3] ENLACE — transición que conecta con el párrafo siguiente o cierra el argumento
```

Un párrafo sin oración tópica no tiene ancla argumentativa: el lector no sabe
qué afirmación debe creer al terminar de leerlo.

---

## Tamaño y densidad del párrafo

### Rangos recomendados
- **Líneas por párrafo:** 4–7 (parámetro visual primario)
- **Frases por párrafo:** 3–4 como norma; hasta 6 en párrafos de análisis denso
- **Palabras por oración:** 15–25 de media; máximo absoluto 30
- **Párrafos por página:** 3–8 (promedio: 5)

Buscar homogeneidad geométrica: bloques regulares de 4–7 líneas dan una página
equilibrada. Quedan excluidos los párrafos-lata (saturan la retención visual) y
los párrafos atómicos de una sola frase.

### Tipos problemáticos

**Párrafo-frase** (1 sola oración): señal de idea incompleta o de que
la idea debería fusionarse con el párrafo anterior.
```
Problemático: "La autocorrelación espacial fue significativa."
Solución: incorporar al párrafo anterior o desarrollar en 3–4 frases.
```

**Párrafo-lata** (> 1 página): el lector pierde el hilo. Identificar si hay
dos o más argumentos distintos → dividir, asignando una oración tópica a cada uno.

**Señal de párrafo-lata:** el párrafo menciona más de un resultado o aspecto
metodológico sin una oración tópica que los unifique.

---

## Oración tópica — cómo construirla

### Características
- Declara la afirmación, no los datos
- Tiene verbo activo y sujeto claro
- No empieza con "En el año X…" ni con un dato

### Patrones por sección

**Resultados:**
```
"[Variable] mostró / exhibió / reveló [patrón o tendencia] [dirección]."
"La accesibilidad resultó ser el predictor de mayor peso (β = −.38)."
"Los coeficientes del modelo SAR fueron estadísticamente significativos
 para cinco de los seis atributos espaciales analizados."
```

**Discusión:**
```
"Este resultado es consistente con / contrasta con [literatura]."
"El efecto identificado es mayor en el área de estudio que en los casos
 comparados, lo que sugiere [interpretación]."
```

**Marco teórico:**
```
"[Autor, año] propone que [afirmación central de la fuente]."
"El marco teórico de referencia establece que [principio], lo que implica
 [consecuencia para este estudio]."
```

---

## Marcadores textuales — catálogo funcional

Los marcadores deben aparecer en posición inicial de oración o después de
punto y coma. Evitar acumular varios en la misma frase.

### Para señalar orden y secuencia
- en primer lugar / en segundo lugar / finalmente
- a continuación / seguidamente / luego
- antes de / tras / una vez que

### Para contrastar e introducir objeciones
- sin embargo / no obstante / ahora bien
- por el contrario / en cambio
- aunque / si bien / a pesar de que / aun cuando
- con todo / aun así / con eso y con todo

### Para indicar causa-efecto y consecuencia
- por consiguiente / en consecuencia / de ahí que
- por lo tanto / así pues / de modo que
- debido a esto / a raíz de esto / como resultado

### Para añadir información o confirmar
- además / asimismo / del mismo modo / igualmente
- también / incluso / es más / más aún
- en la misma línea / en esa dirección

### Para aclarar, reformular o hacer énfasis
- es decir / esto es / en otras palabras
- hay que destacar que / conviene subrayar que
- en efecto / precisamente / sobre todo

### Para restringir o matizar
- ahora bien / no obstante
- con todo / salvo que / excepto cuando
- en la medida en que / siempre que

### Para concluir o resumir
- en conclusión / en síntesis / en suma
- para concluir / a modo de síntesis
- lo anterior sugiere que / los resultados indican que

---

## Coherencia referencial — anáforas y pronombres

### La regla del referente cercano
Un pronombre solo sustituye de forma segura a su referente si ambos están
dentro de las dos frases anteriores. Si hay más distancia → repetir el sustantivo
o usar un sinónimo descriptivo.

```
Ambiguo (referente lejano):
"El modelo SAR y el modelo SEM se estimaron con el paquete spreg de Python.
 Se construyó la matriz de pesos espaciales tipo queen, estandarizada por fila.
 Se evaluó la estabilidad de los parámetros mediante jackknife. Él mostró
 mejor ajuste según el criterio AIC."

Claro:
"...Se evaluó la estabilidad de los parámetros mediante jackknife. El modelo
 SAR mostró mejor ajuste (AIC = 1243 vs. AIC_SEM = 1389)."
```

### "Este/esta" vs. "dicho/a" vs. "el/la mencionado/a"
- **este/esta** → referencia clara, neutro
- **dicho** → ligeramente formal; aceptable en español académico con moderación
- **el mencionado / la mencionada** → puede sonar redundante; preferir "este/esta"
- **el anterior** → correcto para referencia a lo que precede inmediatamente

### Ambigüedad de "el/la mismo/a"
Evitar usar "el mismo" para sustituir un sustantivo cuando hay otro sujeto posible:
```
Ambiguo:  "El modelo OLS y el SAR se aplicaron al conjunto de datos. El mismo
           mostró heterocedasticidad."
Claro:    "El modelo OLS y el SAR se aplicaron al conjunto de datos. El OLS
           mostró heterocedasticidad (prueba de Breusch-Pagan, χ² = 18.3, p < .001)."
```

---

## Progresión temática (tema-rema)

La cohesión lineal se logra encadenando la información: el **rema** (información
nueva, al final de una oración) se convierte en el **tema** (información conocida,
al inicio) de la oración siguiente.

```
Débil (sin encadenamiento):
"El modelo SAR incorpora un rezago espacial. La heterocedasticidad afecta al OLS.
 El área de estudio exhibe heterogeneidad espacial significativa."

Con progresión tema-rema:
"El modelo SAR incorpora un rezago espacial [rema]. Ese rezago [tema] captura la
 dependencia que el OLS ignora [rema]. Dicha dependencia [tema] caracteriza la
 heterogeneidad espacial del área de estudio."
```

Patrones válidos: tema constante (mismo tema, distintos remas) o progresión
lineal (rema → tema). Lo que se evita es el salto sin puente entre oraciones.

## Unidad del eje argumental

Un párrafo descriptivo desarrolla un solo eje. Las digresiones conceptuales o
los datos secundarios se derivan a una nota al pie o a un párrafo aparte, no se
intercalan en el cuerpo central donde rompen la progresión.

## Redacción en positivo

Reformular frases negativas en construcciones afirmativas cuando sea posible:

| Negativo | Positivo |
|----------|---------|
| No es posible ignorar que… | Es necesario considerar que… |
| No hay evidencia en contra de… | La evidencia respalda que… |
| El modelo no mostró independencia espacial. | Los residuos exhibieron dependencia espacial. |
| No se encontraron diferencias significativas. | Las diferencias no alcanzaron significancia estadística. |
| No es casualidad que… | Es significativo que… |
| No cabe duda de que… | Resulta claro que… |

**Nota:** algunos resultados negativos deben formularse en negativo por precisión
estadística ("no se encontraron diferencias significativas entre X e Y" es la forma
correcta en ese caso, no una alternativa positiva forzada).

---

## Técnicas de preescritura — cuando la estructura no aparece

### Brainstorming de ideas
1. Anotar en papel toda la información relacionada con el argumento del párrafo,
   sin filtro (palabras, frases, datos, citas)
2. Identificar la afirmación central que resume el argumento
3. Ordenar el resto como evidencia, contexto o transición
4. Escribir el párrafo a partir de ese esquema

### Las 6 preguntas periodísticas (para desbloquear secciones)
Aplicar a cualquier sección en blanco:
- **Quién:** ¿quiénes son los actores o unidades de análisis?
- **Qué:** ¿qué se observa, mide o propone?
- **Cuándo:** ¿en qué período?
- **Dónde:** ¿en qué área geográfica o contexto?
- **Cómo:** ¿con qué método o procedimiento?
- **Por qué:** ¿qué explica el hallazgo?

### El cubo (para análisis multidimensional de un concepto)
Útil para el marco teórico y la discusión:
- **Descríbelo:** qué es / en qué consiste
- **Compáralo:** en qué se parece o diferencia de otros conceptos similares
- **Relaciónalo:** con qué otros fenómenos está vinculado
- **Analízalo:** cuáles son sus componentes o dimensiones
- **Aplícalo:** cómo se manifiesta en el caso de estudio (área de estudio concreta)
- **Arguméntalo:** por qué es relevante para los objetivos de la tesis
