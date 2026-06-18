---
name: eda-esda-diagnostico
description: >
  Use this skill when the user wants to audit, diagnose, profile, or explore a
  raw geospatial dataset BEFORE econometric modeling — triggers include "audita
  este shapefile/GeoJSON", "EDA espacial", "ESDA", "revisa el CRS", "qué matriz
  de pesos uso", "calcula Moran global", "diagnostica la distribución de
  precios", "antes de modelar revisa mis datos". Covers CRS and geometry audit,
  visual-density diagnosis, non-spatial EDA, spatial weights (W) construction,
  connectivity-graph validation, global Moran's I against CSR, and optimal class
  selection (Fisher-Jenks / ADCM). Produces reproducible Python (GeoPandas,
  libpysal, esda, mapclassify, seaborn).
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
---

# Skill 1 — Diagnóstico estadístico y exploración espacial (EDA + ESDA)

Este skill es un motor de auditoría, no un menú. Opera con reglas de decisión
implacables: limpia el terreno estadístico-geográfico antes de que cualquier
dato entre a un modelo econométrico. Genera código Python ejecutable e
incrusta el criterio que justifica cada elección.

## Principio operativo

NO preguntar al usuario qué método quiere por defecto. Inspeccionar el dataset,
aplicar las reglas de cada módulo y declarar explícitamente cada decisión con su
justificación. Solo escalar al usuario cuando una regla detecta un conflicto
irresoluble (p. ej. CRS geográfico sin metadato de zona UTM).

## Orden de ejecución (no negociable)

```
[Dataset crudo] → [Audita CRS y geometría] → [Analiza la curva de distribución (EDA)]
   → [Estructura la red de vecindad W] → [Valida barreras con grafo de conectividad]
   → [Prueba CSR con Moran global] → [Optimiza clases con Fisher-Jenks/ADCM]
```

Ejecutar los módulos en este orden. Cada módulo consume artefactos del anterior.
Al terminar, entregar: dataset reproyectado, `W` auditada y estandarizada,
diagnóstico de distribución + transformación recomendada, resultado de Moran
global y cuadrantes del diagrama de Moran. Estos alimentan a los Skills 2-4.

## Módulo 1 — Ingesta, perfilado geométrico y control de escala

**Auditoría del tipo de entidad (object vs. field view).** Clasificar el
dataset antes de tocarlo:
- Puntos de scraping inmobiliario → entidades discretas (objetos), alta densidad,
  riesgo de superposición (*cluttering*).
- Manzanas / distritos → unidades de agregación espacial (campos).

**Interrogación del CRS.** Exigir proyección métrica local. Para el Cusco,
UTM 19S = EPSG:32719. Si el dataset llega en grados decimales (EPSG:4326), FRENAR
y reproyectar antes de cualquier cálculo de distancia, área o vecindad — de lo
contrario la curvatura terrestre distorsiona todo. Nunca calcular `W` ni
distancias sobre coordenadas geográficas.

**Diagnóstico de densidad visual.**
- Puntos: calcular el índice de vecino más cercano (NNI). Si la densidad satura
  el centro urbano, PROHIBIR el render de marcadores simples. Aplicar
  generalización: agregación a malla hexagonal (H3) o superficie continua por
  Kernel Density Estimation (KDE).
- Polígonos: calcular el ratio de áreas (máx/mín). Si hay polígonos enormes en la
  periferia rural y diminutos en el núcleo, advertir que una coropleta normal crea
  una *mentira visual* (el color periférico domina el ojo). Recomendar centroides
  con símbolos proporcionales.

**MAUP y principio de tinta proporcional (Openshaw, Goodchild, Wilke).** Con datos
agregados, los límites administrativos alteran el resultado (efecto de escala y de
zonificación). La tinta/color debe ser proporcional al VALOR, no al tamaño del
polígono. Si las áreas son muy heterogéneas, desaconsejar la coropleta clásica y
ofrecer alternativas: cartograma (deformar por una variable de control:
transacciones, población), malla regular H3 (densidades puras comparables), o
superficie picnofiláctica de Tobler (gradiente continuo que CONSERVA la masa por
polígono). Tratar como recomendación fuerte, no como bloqueo absoluto.

**Patrón de puntos multiescala (Ripley).** Con puntos crudos de scraping, NO fijar
un radio KDE arbitrario. Correr la función K de Ripley / L de Besag contra la
envolvente de CSR; la distancia de máxima desviación positiva es el radio óptimo
de interacción → usarlo como ancho de banda KDE y como banda de distancia
econométrica.

Código y umbrales: `references/modulo1-ingesta-geometria.md`,
`references/modulo1b-maup-alternativas.md`, `references/modulo1c-patron-puntos-ripley.md`.

## Módulo 2 — Perfilado estadístico no espacial (motor EDA)

**Graficar primero, modelar después (Tobler).** El ojo humano es el mejor
detector de patrones espaciales. Antes de cualquier modelo, mapear cada variable
independiente por separado en escala de grises y contar la historia de la ciudad
solo mirando esas imágenes crudas. Si no se ve una lógica espacial a simple vista,
ningún GWR/MGWR la inventará. La matemática cuantifica y valida estructura, no la
crea donde no existe.

Antes de mirar el mapa, interrogar la tabla de atributos.

**Asimetría y colas largas.** Precio del suelo y densidades casi nunca son
normales: sesgo a la derecha extremo. Ejecutar pruebas de asimetría (skewness,
kurtosis, Shapiro/Jarque-Bera). Skewness alto ⇒ la varianza destruirá la
eficiencia de un OLS global.

**Prescripción de transformación.** Determinar si la variable necesita
transformación (log o Box-Cox) para estabilizar varianza, ANTES de mapear o
modelar. Registrar lambda de Box-Cox para herencia al Skill 3.

**Validación bivariada con seaborn.**
- `jointplot` de alta densidad: dependiente (precio) vs. continuas (pendiente,
  distancia al centro), con histogramas marginales para ver dónde se acumulan
  las observaciones.
- `boxplot`/`violinplot` condicional: para categóricas (zonificación, distrito),
  ordenados por la mediana del precio. Revela heterocedasticidad entre
  submercados de un vistazo.
- Anti-sobreploteo: con miles de transacciones (scraping), el scatter sólido es un
  gráfico ciego (no distingue 10 de 5000 puntos encimados). Sobre el umbral, usar
  hexbinning (color = conteo por celda) o contornos KDE-2D para aislar los picos
  reales de acumulación del mercado.

Código y umbrales: `references/modulo2-eda-distribucion.md`.

## Módulo 3 — Topología del espacio (libpysal)

Definir matemáticamente qué significa "vecino". No elegir por defecto; aplicar
reglas:

- Polígonos continuos (manzanas urbanas) → contigüidad: Queen (esquinas y lados)
  o Rook (solo lados compartidos). Queen por defecto en tejido urbano denso.
- Puntos dispersos o polígonos muy irregulares separados por barreras → KNN o
  bandas de distancia crítica. Garantizar que ninguna observación quede aislada
  (peso cero).

**Auditoría de barreras e islas.** Cusco metropolitano está condicionado por
topografía agresiva, ríos y zonas arqueológicas protegidas. Inspeccionar `W` en
busca de islas estadísticas (`w.islands`) y componentes desconectados.

**Aplicación visual.** Graficar el grafo de conectividad: líneas uniendo
centroides de vecinos sobre el mapa base. Si una arista cruza un cerro empinado o
un río sin puente uniendo barrios realmente desconectados, ALERTAR que la matriz
está mal calibrada y sugerir recortar la vecindad por accesibilidad real.
Estandarizar la matriz por filas (`transform='r'`) antes de usarla.

Código y umbrales: `references/modulo3-pesos-espaciales.md`.

## Módulo 4 — Autocorrelación global (ESDA)

¿Las manchas de color son significativas o azar? Resolver la subjetividad.

**Rezago espacial (spatial lag).** Calcular el promedio ponderado de los vecinos
para cada observación (`libpysal.weights.lag_spatial`). Limpia el ruido local y
revela la tendencia del entorno.

**Moran global.** Contrastar el Índice de Moran contra la hipótesis nula de
Aleatoriedad Espacial Completa (CSR), con inferencia por permutaciones (≥999).
- p < 0.05 e I positivo ⇒ concentración / segregación (clusters): altos rodeados
  de altos, bajos de bajos.
- I negativo significativo ⇒ dispersión tipo tablero de ajedrez.

**Diagrama de dispersión de Moran.** Eje X = variable estandarizada (z), eje Y =
su rezago espacial. La pendiente de la regresión = Moran global. Dividir en
cuatro cuadrantes — HH, LL, HL, LH — que alimentan al Skill 2.

**LISA (Moran local).** Tras el global, calcular `esda.moran.Moran_Local` para
descomponer el territorio observación por observación y graficar con
`splot.esda.moran_scatterplot` / `lisa_cluster`. Segmentar en los cuatro
cuadrantes unívocos (HH, LL, HL, LH) reteniendo SOLO los significativos
(`p_sim < 0.05`). Esta partición — geometría × cuadrante × significancia — es el
insumo directo de la máscara cartográfica del Skill 2.

**Gi* vs. LISA — regla de decisión (Getis-Ord).** No son intercambiables. LISA
(`Moran_Local`) mide la SIMILITUD de una unidad con su entorno → halla clusters
(HH/LL) y anomalías (HL/LH). Gi* (`esda.getisord.G_Local`) incluye el valor de la
propia unidad → identifica concentraciones ABSOLUTAS (hotspots/coldspots). Para
discutir burbujas especulativas o clusters de alta valoración absoluta frente a la
media de la ciudad, usar Gi* y mapear los Z-scores con cortes en ±1.96
(hotspot/coldspot al 95%), neutro y desaturado en lo no significativo.

Código y umbrales: `references/modulo4-moran-global.md`,
`references/modulo4b-lisa.md` y `references/modulo4c-getis-ord.md`.

## Módulo 5 — Clasificación estadística (mapclassify + Brewer)

Cerebro cartográfico: agrupar continuos en categorías de color sin destruir la
verdad de la muestra.

**Reglas de clasificador:**
- **Fisher-Jenks / Natural Breaks** — OBLIGATORIO por defecto para distribuciones
  irregulares o con saltos bruscos (transiciones comercial→residencial). Minimiza
  varianza intra-clase y maximiza diferencia inter-clase.
- **Cuantiles** — solo si el usuario necesita posiciones relativas (20% más caro
  vs. 20% más barato). Emitir advertencia explícita: "agrupa valores muy
  distantes en los extremos y rompe valores similares en el centro".
- **Head-Tail Breaks** — preferente si la distribución tiene cola pesada extrema
  (ley de potencias: poquísimas manzanas con precios astronómicos). Divide
  iterativamente alrededor de la media.

**Validación ADCM.** Calcular la Desviación Absoluta alrededor de la Mediana de
Clase para 5, 6 y 7 clases. Recomendar el número óptimo donde la ganancia de
ajuste se estabiliza. PROHIBIR más de 7 clases (el ojo ya no las diferencia
cromáticamente).

Código y umbrales: `references/modulo5-clasificacion.md`. Paletas y layout final:
ver Skill 4 (`cartografia-reproducible`).

## Entrega final

Resumir cada decisión con su justificación y exportar los artefactos para el
resto del pipeline. No declarar el diagnóstico completo sin haber corrido los
cinco módulos y verificado que `W` no tiene islas y que Moran tiene inferencia
por permutaciones.
