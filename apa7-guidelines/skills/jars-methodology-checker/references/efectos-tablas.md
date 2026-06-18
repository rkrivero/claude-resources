# Efectos, tablas y figuras para investigación cuantitativa — APA 7, Caps. 7 y 3

## Tamaños de efecto — guía para modelos de regresión

APA 7 exige reportar al menos un tamaño del efecto con IC. Para modelos de
regresión con variable dependiente continua, las opciones más adecuadas son:

| Estadístico | Cuándo usar | Interpretación convencional |
|-------------|-------------|----------------------------|
| R² / R²adj  | OLS — varianza explicada global | .01 pequeño, .09 mediano, .25 grande |
| f² de Cohen | OLS — efecto incremental de un predictor o bloque | .02 peq., .15 med., .35 grande |
| η² parcial  | Comparación entre grupos (ANOVA) | .01 peq., .06 med., .14 grande |
| Pseudo-R² de Nagelkerke | SAR / SEM (sustituto de R² para ML) | igual escala que R² |
| r de punto-biserial | Predictor binario en OLS | mismo umbral que r de Pearson |

**Fórmula de f² para un predictor individual en OLS:**
```
f²(Xi) = (R²_completo - R²_sin_Xi) / (1 - R²_completo)
```

**Cómo reportarlo en texto:**
```
La [variable predictora] explicó un 8% incremental de la varianza en [variable dependiente]
(f² = 0.15, IC 95% [0.09, 0.22]), efecto de tamaño mediano.
```

---

## Regresión jerárquica (bloques de variables)

Presentar en tabla por bloques. Reportar R²Δ y su F-change por bloque:

```
Tabla X
Regresión jerárquica de [variable dependiente] (n = 487)

                        Modelo 1         Modelo 2         Modelo 3
Variable              B      SE        B      SE        B      SE
──────────────────────────────────────────────────────────────────
Constante          8.12    0.45     7.84    0.41     7.63    0.39
— Bloque 1: [Categoría de variables 1]
Variable A         0.013   0.001    0.011   0.001    0.010   0.001
Variable B        −0.009   0.002   −0.007   0.002   −0.006   0.002
— Bloque 2: [Categoría de variables 2]
Variable C                  —       0.003   0.001    0.002   0.001
Variable D (dummy)          —       0.187   0.052    0.165   0.050
— Bloque 3: [Categoría de variables 3]
Variable E                  —        —       —      −0.0003  0.00005
Variable F                  —        —       —      −0.012   0.003
──────────────────────────────────────────────────────────────────
R²                  .31              .43              .52
R²Δ                 —               .12              .09
F-change          109.2***          50.4***          44.7***
df                 2, 484           2, 482           2, 480

Nota. *** p < .001. Variable dependiente: [indicar variable y transformación].
Coeficientes no estandarizados. IC 95% disponibles como material suplementario.
```

---

## Términos de interacción

Reportar siempre los efectos simples (main effects) junto con la interacción.
El coeficiente de interacción va al final del bloque correspondiente:

```
Variable                   B       SE       β       t        p
───────────────────────────────────────────────────────────────
[Variable A]            0.013    0.001    .40     13.0    <.001
[Variable binaria] (dummy) 0.342 0.058    .28      5.9    <.001
[Variable A] × [Bin.]   0.005    0.002    .12      2.5     .013
```

Interpretar: "El efecto de [Variable A] sobre [Variable dependiente] fue mayor
en la [Zona/Grupo A] (B = 0.018) que en la [Zona/Grupo B] (B = 0.013)."

---

## Errores estándar robustos

Cuando se usan HC (heteroskedasticity-consistent) SEs, declararlo explícitamente:

```
Nota. SE reportados son HC3 (errores estándar robustos a heterocedasticidad;
White, 1980). Los resultados con SE convencionales aparecen en la Tabla A1
del Anexo.
```

En texto: `B = 0.32, SE_HC3 = 0.09, t = 3.56, p < .001`

---

## GWR (Geographically Weighted Regression)

### En texto

```
Se estimó un modelo GWR con kernel gaussiano adaptativo (bandwidth óptimo
= 125 vecinos, seleccionado por AIC corregido [AICc]). La variabilidad
espacial de los coeficientes se evaluó con el estadístico de Monte Carlo.
```

### En tabla (resumen de distribución de coeficientes locales)

```
Tabla X
Distribución de coeficientes GWR para las principales variables (n = 487)

Variable          Mín       Q1      Mediana     Q3       Máx    Sig.(%)
────────────────────────────────────────────────────────────────────────
[Variable A]     0.004    0.009     0.013     0.016    0.025     94
[Variable B]    −0.0008  −0.0004   −0.0002  −0.0001   0.0001    78
[Variable C]    −0.018   −0.011    −0.007   −0.003    0.002     61
────────────────────────────────────────────────────────────────────────
Nota. Sig.(%) = porcentaje de ubicaciones con coeficiente local
estadísticamente significativo (p < .05). Bandwidth = 125 (AICc).
R² local mediano = .58. GWR estimado con GWmodel (Lu et al., 2014).
```

---

## Tablas — estructura completa APA 7 (Cap. 7)

### Anatomía de una tabla APA 7

```
Tabla [N]                          ← número arábigo, sin punto
Título en cursiva, sentence case   ← describe exactamente el contenido

Encabezado de columna 1   Col. 2   Col. 3
─────────────────────────────────────────
Fila de datos              x.xx     x.xx
  Subgrupo (sangría)       x.xx     x.xx
─────────────────────────────────────────
Total / Media              x.xx     x.xx

Nota. [Nota general en texto plano.] a [Nota específica a superíndice].
* p < .05. ** p < .01. *** p < .001.
```

### Reglas de notas de tabla

| Tipo | Cuándo | Formato |
|------|--------|---------|
| Nota general | Explica la tabla en su conjunto | `Nota.` en cursiva |
| Nota específica | Referida a celda con superíndice | `a` `b` `c` en cursiva |
| Nota de probabilidad | Niveles de significancia | `*` `**` `***` |

**Orden de las notas:** General → Específicas → Probabilidad.

### Lo que NO va en tablas APA 7

- Líneas verticales (solo horizontales: sobre encabezado, bajo encabezado, al final)
- Sombreado de celdas / bandas de color
- Columna de "Variable" con número de fila
- Abreviaturas sin definir en la nota

### Celdas vacías (APA §7.13)

| Situación | Qué poner |
|-----------|-----------|
| Dato no aplicable (N/A) | Dejar la celda en blanco |
| Dato no obtenido o no reportado | Guion largo `—` |

Si se usa el guion largo, explicar en la **nota general**: `Nota. — indica que el dato no fue recolectado para ese período.`

### Intervalos de confianza en tablas (APA §7.16)

Cuando se reportan estadísticos puntuales (B, ρ, d, r), incluir el IC 95%
en la misma tabla. Opciones:
- Columna separada: `IC 95%` con celdas `[LL, UL]`
- Columnas separadas `LL` y `UL`
- Si el espacio no alcanza, en la nota: `IC 95% disponibles en el Anexo A.`

---

### Checklist de tabla APA 7 (§7.20)

Antes de incluir cualquier tabla en la tesis, verificar:

☐ Número arábigo (`Tabla 1`, sin punto ni abreviatura)
☐ Título en cursiva, sentence case, descriptivo y conciso
☐ Callout en el texto antes de la tabla: "La Tabla 1 presenta…" o `(véase Tabla 1)`
☐ Solo líneas horizontales: encima de la primera fila de encabezados, debajo de la última fila de encabezados, al final de la tabla
☐ Sin líneas verticales
☐ Sin sombreado de celdas
☐ Celdas N/A en blanco; celdas sin dato → `—` con nota general explicativa
☐ Todas las abreviaturas definidas en nota (primera aparición en la tabla)
☐ N reportado en la tabla o en la nota general
☐ Asteriscos de significancia definidos en nota de probabilidad
☐ IC 95% incluido si se reportan estadísticos puntuales
☐ Nota general → nota específica → nota de probabilidad (en ese orden)
☐ Cada nota en línea propia; múltiples notas del mismo tipo separadas con `. ` (punto + espacio)
☐ Tabla referenciada en el texto (no huérfana)
☐ Si es adaptada o reproducida: atribución en nota y permiso gestionado

---

## Figuras — estructura APA 7 (Cap. 7)

```
[Imagen/gráfico]

Figura [N]
Título descriptivo en cursiva, sentence case.

Nota. Explicación adicional si es necesaria. Fuente si es adaptada:
De "Título del trabajo original" por A. Autor, Año, Nombre de la Revista,
vol(núm), p. XX (https://doi.org/xxxxx). Copyright Año por Nombre del titular.
Adaptada con permiso.
```

### Tipos de figuras en tesis espacial

| Figura | Qué incluir en la nota |
|--------|------------------------|
| Mapa de área de estudio | CRS, fuente de la cartografía base, fecha, escala gráfica |
| Diagrama de dispersión | n, línea de regresión si aplica |
| Mapa de calor LISA | nivel de significancia (p), tipo de W, permutaciones |
| Diagrama de flujo metodológico | software usado en cada etapa |
| Gráfico de barras de error | qué representa la barra de error (DE, IC 95%) |

---

## Materiales suplementarios

Cuando los resultados completos exceden el espacio del cuerpo principal,
mencionar en texto:

```
Los coeficientes completos del modelo incluyendo todas las variables de control
se presentan en la Tabla A1 del Anexo Metodológico.
```

Y en la nota de la tabla abreviada:
```
Nota. Solo se muestran los predictores de interés teórico. La tabla completa
con todas las covariables figura en la Tabla A1 del Anexo.
```
