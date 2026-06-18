# Reporte de estadísticos en texto — APA 7, §6.4–6.5 + JARS-Quant

## Principios generales

- Punto decimal (no coma): `M = 4.5`, `p = .03`
- Cero antes del decimal solo si el valor puede superar 1: `t = 0.45`
- Sin cero antes si el rango es 0–1: `p = .04`, `r = .67`, `β = .31`
- Dos decimales para la mayoría de estadísticos (excepción: p con 3 decimales)
- Espacio entre símbolo y valor: `M = 45.3`, no `M=45.3`
- Símbolos en cursiva: *M*, *DE*, *t*, *F*, *p*, *r*, *β*, *R*²

## Estadísticos descriptivos

```
M = 45.32, DE = 12.1, n = 487
Mediana = 42.0, RIC = [35.0, 55.0]
```

## Correlación de Pearson

```
r(485) = .54, p < .001
```

## Prueba t

```
t(485) = 3.21, p = .001, d = 0.29, IC 95% [0.11, 0.47]
```

## ANOVA

```
F(2, 484) = 12.43, p < .001, η² = .05
```

## Regresión OLS (en texto)

```
B = 0.32, SE = 0.08, t(484) = 4.00, p < .001, β = .28
R² = .47, R²adj = .45, F(5, 481) = 85.2, p < .001
```

## Regresión espacial — SAR (Spatial Autoregressive)

```
ρ = 0.43, SE = 0.09, z = 4.78, p < .001
Log-verosimilitud = −1234.5, AIC = 2489.0
```

## Regresión espacial — SEM (Spatial Error Model)

```
λ = 0.51, SE = 0.10, z = 5.10, p < .001
Log-verosimilitud = −1228.3, AIC = 2476.6
```

## Pruebas de diagnóstico espacial

```
Moran's I (residuos OLS) = 0.18, p < .001
LM-lag = 23.4, p < .001
LM-error = 18.9, p < .001
LM-lag robusto = 8.2, p = .004
LM-error robusto = 3.7, p = .055
→ Árbol de decisión: SAR preferido
```

## Moran's I global

```
Moran's I = 0.34, z = 8.21, p < .001 (permutaciones: 999)
```

## Tablas de regresión — estructura mínima APA 7

```
Tabla X
Coeficientes del modelo de regresión (n = 487)

Variable              B        SE       β       t       p
─────────────────────────────────────────────────────────
Constante          8.234     0.421             19.56   <.001
Atributo A         0.012     0.001    .41      12.0    <.001
Distancia (m)     −0.0003   0.00005  −.28     −6.0    <.001
Variable C        −0.008     0.002   −.15     −4.0    <.001
─────────────────────────────────────────────────────────
R² = .52, R²adj = .51

Nota. Variable dependiente: [indicar la variable dependiente y su transformación].
Coeficientes no estandarizados; β = coeficientes estandarizados.
```

## Valores p

- Reportar valor exacto cuando es ≥ .001: `p = .043`, `p = .128`
- Usar `p < .001` cuando el valor es menor (no `p = .000`)
- No usar `p = n.s.` — reportar el valor exacto

---

## Intervalos de confianza (IC / CI)

Formato APA 7 para IC 95% en texto:
```
IC 95% [0.24, 0.41]
```
(corchetes, sin "de" ni coma decimal — usar punto)

En tablas: columnas separadas para LL y UL, o una sola columna con `[LL, UL]`.

---

## Regresión SLX (Spatial Lag of X)

```
El modelo SLX incluye los rezagos espaciales de los predictores (WX) como
covariables adicionales. El efecto directo del área fue B = 0.012 (SE = 0.001,
p < .001) y su rezago espacial WÁrea = 0.007 (SE = 0.002, p = .002),
indicando efectos de desbordamiento positivos a las propiedades vecinas.
```

---

## Errores estándar robustos (HC)

Cuando los residuos exhiben heterocedasticidad (Breusch-Pagan p < .05),
reportar con SE robustos y declararlo:

```
Se detectó heterocedasticidad en los residuos (Breusch-Pagan χ²(5) = 23.4,
p < .001), por lo que se reportan errores estándar HC3 (Long & Ervin, 2000).
```

---

## Prueba de Lagrange Multipliers (árbol de decisión)

Flujo de decisión Anselin & Florax (1995) para selección de modelo espacial:

```
1. Estimar OLS y calcular LM-lag y LM-error
2. ¿Ambos no significativos? → OLS es adecuado
3. ¿Solo LM-lag significativo? → SAR
4. ¿Solo LM-error significativo? → SEM
5. ¿Ambos significativos? → comparar versiones robustas:
   - LM-lag robusto > LM-error robusto → SAR
   - LM-error robusto > LM-lag robusto → SEM
   - Ambos robustos significativos → SDM o SARAR

Formato de reporte:
LM-lag = 23.4 (p < .001), LM-error = 18.9 (p < .001);
versiones robustas: LM-lag = 8.2 (p = .004), LM-error = 3.7 (p = .055).
Conclusión: SAR preferido por Anselin-Florax.
```

---

## Índice de Moran local (LISA) en texto

```
El análisis LISA identificó 87 unidades en clúster alto-alto (HH) y
34 en clúster bajo-bajo (LL) con p < .05 (999 permutaciones), concentradas
en las zonas [describir la localización en función del área de estudio].
```

---

## Diagnóstico de multicolinealidad

```
Los factores de inflación de varianza (FIV) oscilaron entre 1.12 y 3.84,
todos por debajo del umbral convencional de 10 (o de 5 para umbrales
más conservadores), descartando problemas graves de multicolinealidad.
```

---

## Prueba de Breusch-Pagan (heterocedasticidad)

```
La prueba de Breusch-Pagan rechazó la hipótesis de homocedasticidad
(χ²(k) = 23.4, p < .001), lo que motivó el uso de errores estándar
robustos HC3.
```

---

## Estadísticos de bondad de ajuste — resumen comparativo

| Modelo | Estadístico preferido | Fórmula / nota |
|--------|----------------------|----------------|
| OLS | R², R²adj, F global | — |
| SAR / SEM | Log-verosimilitud, AIC, BIC, Pseudo-R² | Nagelkerke = 1 − (L₀/L)^(2/n) |
| GWR | AICc, R² local mediano | AICc corregido para muestras pequeñas |
| MGWR | AICc, R² local mediano, bandwidths por variable | Un bandwidth por predictor |
| Regresión jerárquica | R²Δ con F-change por bloque | — |

Mayor log-verosimilitud (menos negativo) y menor AIC/BIC = mejor ajuste.

---

## MGWR (Multiscale Geographically Weighted Regression)

### En texto — reporte mínimo requerido

```
Se estimó un modelo MGWR con kernel gaussiano adaptativo. Los bandwidths
óptimos varían por variable: [Var A] = 45 vecinos (proceso localizado),
[Var B] = 312 vecinos (proceso cuasi-global), seleccionados por AICc
corregido. La variabilidad espacial de los coeficientes se evaluó con el
test Monte Carlo de no-estacionariedad.
```

### En tabla — distribución de coeficientes locales MGWR

```
Tabla X
Distribución de coeficientes locales MGWR (n = [N])

Variable       Bandwidth   Mín      Q1     Mediana    Q3      Máx    Sig.(%)
────────────────────────────────────────────────────────────────────────────
[Variable A]      45      0.004   0.009    0.013    0.016   0.025     94
[Variable B]     312     −0.008  −0.004   −0.002   −0.001   0.001     78
[Variable C]      67     −0.018  −0.011   −0.007   −0.003   0.002     61
────────────────────────────────────────────────────────────────────────────
Nota. Sig.(%) = porcentaje de ubicaciones con coeficiente local significativo
(p < .05). MGWR estimado con mgwr [versión] (Fotheringham et al., 2017).
R² local mediano = [valor]. AICc = [valor].
```

### Test de no-estacionariedad (Monte Carlo)

```
La prueba Monte Carlo (n = 999 permutaciones) confirmó variabilidad espacial
significativa en los coeficientes de [Variable A] (p = .003) y [Variable B]
(p < .001), pero no en [Variable C] (p = .213), lo que sugiere que [Variable C]
opera como un proceso espacialmente estacionario.
```

### Comparación GWR vs. MGWR

```
Tabla X
Comparación de ajuste GWR y MGWR

Modelo    AICc       R² local (mediana)   Bandwidths (rango)
─────────────────────────────────────────────────────────────
GWR       [valor]    [valor]              [valor] (único)
MGWR      [valor]    [valor]              [rango: min–max]
─────────────────────────────────────────────────────────────
Nota. ΔAIC c < −2 indica mejora sustancial. MGWR preferido si permite
escalas distintas por variable sin pérdida de ajuste.
```

---

## Clustering espacial no supervisado — reporte de resultados

```
Se aplicó HDBSCAN (min_cluster_size = [k]) sobre las coordenadas proyectadas
de las [n] observaciones. Se identificaron [C] clusters con un Silhouette
Score de [valor] y [ratio]% de observaciones clasificadas como ruido.
La coherencia espacial de los clusters fue confirmada por el Índice de
Moran sobre las etiquetas (I = [valor], p [= / <] [valor], 999 permutaciones).
```

---

## Análisis espacio-temporal — reporte de hot spots

```
Se construyó un cubo espacio-temporal de [n] unidades × [t] períodos
([frecuencia]: [período inicio] – [período fin]). Aplicando el test de
Mann-Kendall por celda con corrección FDR (Benjamini-Hochberg), se
identificaron [n_hs] unidades como hot spots significativos (p_adj < .05):
[n] persistentes, [n] intensificantes y [n] emergentes. [n_cs] unidades
fueron clasificadas como cold spots.
```
