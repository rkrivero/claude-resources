# Ingeniería sintáctica de la frase — umbrales y orden

## Umbrales de extensión

| Parámetro | Valor |
|-----------|-------|
| Longitud media de oración | 15–25 palabras |
| Límite máximo absoluto | 30 palabras por unidad sintáctica |
| Longitud máxima de inciso | 15 palabras |
| Niveles de subordinación por enunciado | 2 máximo |
| Adverbios en -mente | 1 por página |

Si una oración supera las 30 palabras, fragmentar con punto y seguido. Si un
inciso supera 15 palabras, convertirlo en oración independiente.

---

## Orden canónico (lineal)

Construir la secuencia respetando el orden básico del español:

```
Sujeto + Verbo + Complemento Directo + Complemento Indirecto + Circunstanciales
```

Los complementos circunstanciales (tiempo, lugar, modo) van **al final** de la
oración, no al principio, para no posponer la aparición del sujeto y el verbo.

```
Fragmentado (CC antepuesto largo):
"Durante el período de recolección de datos comprendido entre enero y
 diciembre de 2023, los precios se mantuvieron estables."

Canónico:
"Los precios se mantuvieron estables durante 2023."
```

---

## Focalización inicial

El núcleo de la información —el dato crítico o el hallazgo— debe aparecer dentro
de las **primeras cuatro palabras** de la oración. El comienzo es la posición
que el lector lee primero y recuerda mejor.

```
Núcleo tardío (débil):
"En el contexto del análisis de regresión espacial realizado, la accesibilidad
 resultó ser la variable más influyente."

Núcleo temprano (fuerte):
"La accesibilidad fue la variable más influyente en la regresión espacial."
```

---

## Ubicación de complementos extensos

Las subordinadas largas o aclaratorias van al final del período, ordenadas de
menor a mayor complejidad. Nunca flotando ni inconexas: toda subordinada opera
como extensión del predicado principal.

```
Pesado (subordinada intermedia):
"Los predios que se ubican en zonas de mayor pendiente, lo que dificulta la
 edificación, presentan precios menores."

Mejor (subordinada al final):
"Los predios de mayor pendiente presentan precios menores, porque la pendiente
 dificulta la edificación."
```

---

## Control de la profundidad de subordinación

Máximo **dos niveles** de subordinación por enunciado. Una tercera cláusula
subordinada dentro de otra subordinada anula la legibilidad: fracturar con punto.

```
Tres niveles (ilegible):
"El modelo, que incluye variables que capturan atributos que los compradores
 valoran, mostró buen ajuste."

Fracturado:
"El modelo incluye variables de los atributos que valoran los compradores.
 Su ajuste fue bueno (R² = .52)."
```

**Evitar el encadenamiento de relativas** con "que… que… que…" sucesivos.

---

## Pantallas lingüísticas — prohibición sujeto/verbo

No insertar elementos explicativos entre el bloque del sujeto y el verbo
principal, sin importar la longitud del sujeto. Tampoco entre artículo y
sustantivo, ni entre sustantivo y su adjetivo.

```
Con pantalla (sujeto y verbo separados):
"Los precios de los inmuebles del Centro Histórico, declarado Patrimonio de la
 Humanidad en 1983, superan el promedio distrital."

Sin pantalla (inciso al final):
"Los precios del Centro Histórico superan el promedio distrital — área declarada
 Patrimonio de la Humanidad en 1983."
```

---

## Prótasis extensas — no aplazar el sujeto

Evitar que la oración comience con estructuras condicionales, concesivas o
temporales largas que pospongan el sujeto y el verbo más allá del primer tercio.
Las premisas de contexto van al final o en una frase previa.

```
Prótasis larga al inicio:
"Si se considera que los precios exhiben autocorrelación espacial significativa
 y que los residuos del OLS violan el supuesto de independencia, se estimó SAR."

Reordenado:
"Se estimó un modelo SAR. Los precios exhiben autocorrelación espacial
 significativa y los residuos del OLS violan el supuesto de independencia."
```

---

## Zonas de baja fricción para los incisos

Si una aclaración entre comas o guiones es necesaria, situarla donde menos
fracture la continuidad: tras el complemento directo o al cierre de la frase.

Posiciones prohibidas para incisos:
- Entre artículo y sustantivo
- Entre sustantivo y su adjetivo calificativo
- Entre sujeto y verbo

```
Inciso bien colocado (tras el CD):
"Se estimó el modelo espacial con 487 observaciones, todas verificadas
 manualmente contra la base de datos oficial."
```

---

## Pasiva refleja espuria — restringir cuando el agente es identificable

Cuando el agente de la acción metodológica es identificable, preferir la voz
activa con el sujeto explícito en lugar de la pasiva refleja impersonal.

```
Pasiva refleja (agente oculto):
"Se considera que el modelo SAR es el más adecuado."

Activa (agente explícito):
"Concluimos que el modelo SAR es el más adecuado."
```

La pasiva refleja es aceptable cuando el foco es el procedimiento y no quién
lo ejecuta: "los shapefiles se reprojectaron a EPSG:32719".

---

## Checklist sintáctico rápido

1. ¿La oración supera 30 palabras? → fragmentar
2. ¿El sujeto y el verbo aparecen en las primeras 4 palabras? → si no, reordenar
3. ¿Hay un circunstancial largo al inicio? → moverlo al final
4. ¿Hay algo entre el sujeto y el verbo? → quitar o mover el inciso
5. ¿Hay más de 2 niveles de subordinación? → fracturar
6. ¿El inciso supera 15 palabras? → convertir en oración independiente
7. ¿Hay relativas encadenadas (que… que… que)? → desarmar
