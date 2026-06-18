---
name: composicion-lienzo-cartografico
description: >
  Use this skill when the user wants to compose, lay out, label, or export a
  finished map figure — triggers include "arma el layout del mapa", "jerarquía
  visual", "balance del espacio vacío", "etiquetas que no se solapen",
  "adjustText", "halos en el texto", "path effects", "mapa base mudo",
  "contextily", "exportar para tesis / impresión / diapositiva", "300 DPI",
  "CMYK vs RGB", "selección de fuentes", "tipografía del mapa", "fuente serif /
  sans-serif", "tracking de etiqueta", "tamaño mínimo de fuente", "posición de
  etiqueta", "upper-right", "etiquetas de área", "color bivariado", "dos
  variables en el mapa", "coeficiente + error estándar", "grilla de colores 3x3",
  "escala gráfica", "flecha de norte redundante", "alineación intencional".
  Orchestrates visual hierarchy (Brewer), label collision + placement by geometry
  type (adjustText), figure-ground contrast via text halos, muted contextual
  basemaps (contextily), font selection (Brewer Ch. 6-7), bivariate color schemes
  (Brewer Ch. 9), and medium-specific export (print A4/Letter vs screen). Consumes
  themed/masked layers from the analysis skills. Produces reproducible matplotlib code.
metadata:
  version: "0.1.0"
  author: "spatial-audit-engine"
---

# Skill 3 — Composición de lienzo, jerarquía visual y control de colisiones

Motor de acabado cartográfico. Toma capas ya estructuradas estadísticamente
(Skill 1) y enmascaradas por significancia econométrica (Skill 2) y las compone
según las reglas de diseño de Cynthia Brewer. Genera código matplotlib
reproducible con el criterio incrustado.

## Principio operativo

El análisis manda; el ornamento se subordina. Prohibir todo elemento que añada
peso visual muerto (cajas negras, fondos opacos, mapas base con texto). Ordenar
con espacio vacío y alineación, no con recuadros. Calibrar la salida al medio
físico real (impresión vs. pantalla) antes de exportar.

## Insumos

- Capa temática significativa (coeficientes enmascarados, R² local, residuos) →
  Skills 1 y 2.
- Componentes marginales obligatorios: escala gráfica, flecha de norte, fuentes/
  metadatos, leyenda.

## Módulo 1 — Jerarquía visual y balance del espacio vacío (matplotlib)

**Evalúa:** el equilibrio y la masa visual del layout. El centro visual se sitúa
~5% por encima del centro geométrico del lienzo. Detecta descentramientos,
alineamientos ambiguos y cajas contenedoras que fragmentan la lectura.

**Aplica:** reglas de Brewer. PROHIBIR el encajonamiento con bounding boxes
negros. Ordenar con espacio vacío (negative space) y alineación a los ejes
invisibles del marco. Jerarquía de lectura en tres niveles:
1. Primer plano — capa econométrica significativa.
2. Segundo plano — referencia territorial y escala.
3. Plano de soporte — título, leyendas, fuentes.

Código: `references/modulo1-jerarquia-layout.md`.

## Módulo 2 — Placement y control de colisiones de etiquetas (adjustText + Brewer)

**Obtiene:** centroides de polígonos / puntos y las cadenas de texto (distritos,
centralidades, barrios históricos).

**Evalúa:** (a) solapamiento entre bounding boxes tipográficos y geometrías;
(b) posición preferida según el tipo de entidad: puntos usan 6 posiciones con
upper-right como prioridad máxima; áreas usan centroide con tracking para cubrir
el polígono; líneas se etiquetan paralelas a su dirección. Detecta zonas de
colapso de legibilidad (centro consolidado vs. periferia dispersa).

**Aplica:** repulsión iterativa con `adjustText` despachada por tipo de geometría.
Fuerza de atracción hacia la coordenada real del dato y repulsión respecto a textos
adyacentes y bordes. En zonas saturadas, desplazar el texto y trazar leader lines
ultradelgadas (0.2–0.5 pt). Combinar con `modulo13-fuentes-tipografia.md` para la
selección de familia tipográfica según tipo de entidad.

Código: `references/modulo2-adjusttext.md`.

## Módulo 3 — Contraste frente-fondo y legibilidad (halos / path effects)

**Obtiene:** anotaciones y leyendas que se superponen a coropletas continuas,
superficies de densidad o tramas morfológicas.

**Evalúa:** contraste cromático y legibilidad contra fondo variable. Detecta
pérdida de nitidez cuando una palabra cruza polígonos de valores extremos
opuestos.

**Aplica:** `matplotlib.patheffects.withStroke`. PROHIBIR fondos opacos cuadrados
tras el texto. Aplicar halo perimetral sutil de 1.5–2.5 pt; color del halo
inverso al del texto (halo blanco/gris tenue para fuente oscura; halo oscuro para
fuente clara). Aísla el texto del ruido sin romper la continuidad de la capa
temática.

Código: `references/modulo3-halos.md`.

## Módulo 4 — Fondos contextuales mudos multiescala (contextily)

**Obtiene:** bounding box geográfico exacto y solicitudes de teselas según el CRS
proyectado.

**Evalúa:** interferencia semántica del mapa base de terceros (colores, redes
viales nativas, etiquetas pre-renderizadas que compiten con los coeficientes).

**Aplica:** protocolo de capa de soporte muda. Usar SOLO servidores en escala de
grises sin texto (`CartoDB.PositronNoLabels`, `Stamen.TonerBackground`).
`alpha` estricto entre 0.4 y 0.6, interpolación bilineal al reescalar. Permite
integrar hillshading o trama vial como variable de accesibilidad visual,
subordinada al análisis. Reproyectar las teselas al CRS de los datos (no al revés).

Código: `references/modulo4-contextily.md`.

## Módulo 5 — Salida según medio y export

**Obtiene:** el destino final — impreso (tesis A4/Carta) o digital (diapositiva/
proyector).

**Evalúa:** distancia de lectura, mezcla sustractiva (CMYK impresión) vs. aditiva
(RGB pantalla), y límites de DPI.

**Aplica:**
- **Impreso (tesis):** fuentes de leyenda/escala 7–9 pt; bordes de polígono
  ultradelgados (`linewidth` 0.2–0.35) en gris/blanco para no saturar áreas
  densas; export vectorial sin pérdida (PDF/EPS) o ráster ≥300 DPI. Verificar
  perfil de color para CMYK.
- **Pantalla (diapositivas):** fuente mínima 12–14 pt; bordes ≥0.75 pt para
  evitar flickering; paletas de alto contraste para proyectores de baja fidelidad.

Código: `references/modulo5-export.md`.

## Módulo 13 — Tipografía cartográfica (Brewer, Cap. 6-7)

**Obtiene:** el conjunto de etiquetas del mapa y el tipo de entidad de cada una
(cultural/administrativa vs. hidrográfica/física).

**Evalúa:** número de familias tipográficas en uso, tamaño de fuente respecto al
medio, presencia de display fonts, incrustación en PDF, necesidad de tracking en
áreas extensas.

**Aplica:** máximo 2 familias (sans-serif para culturales, serif+cursiva para
hidrográficos). Tamaño mínimo 6 pt (impresión) / 12 pt (pantalla). Tracking
(`etiqueta_area_tracking`) para polígonos extensos. Verificar `pdf.fonttype=42`
antes de exportar. PROHIBIR Impact, Giddyup u otras display fonts.

Código: `references/modulo13-fuentes-tipografia.md`.

## Módulo 14 — Color bivariado (Brewer, Cap. 9)

**Obtiene:** dos variables continuas que deben representarse simultáneamente en la
misma geometría (coeficiente GWR + error estándar, densidad + vacancia).

**Evalúa:** combinación de tipos: sequential-sequential, diverging-diverging o
qualitative-sequential. Determina el número de clases (máximo 3×3).

**Aplica:** paleta en grilla 3×3, leyenda como cuadrado de colores con flechas
direccionales por eje. Auditar con `simular_daltonismo` (→ `modulo6-color-accesible.md`).
Acompañar con small multiples univariados para lectores no entrenados.

Código: `references/modulo14-color-bivariado.md`.

## Reglas transversales de semiótica y acceso

Auditorías que se aplican a CUALQUIER figura antes de exportar (recomendaciones
fuertes, no bloqueos):

- **Semiología gráfica (Bertin).** Hacer coincidir el nivel de medición con la
  variable visual. Categórico/nominal (tipologías de `momepy`, usos de suelo) →
  SOLO tono (color) o forma; PROHIBIR luminosidad (value) y tamaño, que el ojo
  lee como orden/jerarquía. Luminosidad y tamaño se reservan a cuantitativas
  ordenadas (precio, densidad). Ver `references/modulo7-bertin-semiotica.md`.
- **Color accesible y trampa del arcoíris (Healy, Wilke, Brewer).** ~8% de
  hombres con deficiencia cromática. VETAR `jet`/`rainbow` y rojo-verde: su
  luminancia no es monótona e inventa "muros" donde el dato cambia suave. Usar
  divergentes `RdBu`/`PuOr` (luminosidad monótona hacia el centro neutro) y
  secuenciales perceptualmente uniformes (`viridis`, `magma`, `plasma`,
  `inferno`); auditar con simulador de daltonismo y verificar en escala de grises.
  Ver `references/modulo6-color-accesible.md`.
- **Small multiples (Tufte).** Comparar modelos (OLS/SAR/SEM/GWR/MGWR) en un panel
  de mapas idénticos: mismo encuadre, mismos cortes `mapclassify` y misma paleta;
  varía solo el parámetro estimado. Ver `references/modulo8-small-multiples.md`.
- **Simplificación cartográfica (Robinson).** Generalizar capas base
  (Douglas-Peucker vía `shapely.simplify`) para quitar ruido de alta frecuencia
  (huellas de edificios, vías secundarias) que compite con los gradientes
  econométricos. Ver `references/modulo9-simplificacion.md`.
- **Data-ink ratio / anti-chartjunk (Tufte).** Cada gota de tinta debe comunicar
  dato. Eliminar marcos dobles, cuadrículas gruesas y bordes negros (en zonas de
  manzanas diminutas el negro borra el color del dato → bordes blancos/grises
  `alpha≈0.3`, `linewidth≈0.2`). Norte = línea vertical simple con "N"; escala =
  línea delgada con 2–3 marcas, sin bloques alternados gigantes. Ver
  `references/modulo10-data-ink.md`.
- **Relación de aspecto / banking to 45° (Cleveland).** En curvas de tendencia
  (`seaborn.lineplot` precio vs. pendiente/distancia), el `figsize` no es un
  accidente del software: ajustar la relación de aspecto para que el ángulo medio
  de los segmentos clave se acerque a 45°, donde el ojo juzga pendientes con máxima
  precisión. Ver `references/modulo11-banking-aspect.md`.
- **Leyenda con mini-histograma (efecto Maquiavelo de las clases).** Agrupar con
  Fisher-Jenks crea fronteras que el ojo lee como "saltos mágicos". Acompañar la
  leyenda de la coropleta con un mini-histograma de la variable cruda, con las
  barras coloreadas según las clases del mapa, para mostrar cuántos datos caen en
  cada corte. Ver `references/modulo12-leyenda-histograma.md`.
- **Selección de fuentes y tipografía (Brewer, Cap. 6-7).** Máximo 2 familias por
  mapa (sans-serif para culturales, serif para hidrográficos/físicos). Tamaño mínimo
  6 pt impresión / 12 pt pantalla. Tracking para etiquetas de área. PROHIBIR display
  fonts. Incrustación PDF via `pdf.fonttype=42`. Ver `references/modulo13-fuentes-tipografia.md`.
- **Color bivariado (Brewer, Cap. 9).** Para dos variables simultáneas (coeficiente +
  error estándar, densidad + vacancia): grilla 3×3 sequential-sequential con leyenda
  en forma de cuadrado de colores. No mezclar con univariados; auditar con
  `simular_daltonismo`. Ver `references/modulo14-color-bivariado.md`.

## Entrega final

Una figura compuesta, etiquetada sin colisiones, con halos donde haga falta,
mapa base mudo subordinado y export calibrado al medio declarado. Confirmar el
medio (impreso vs. pantalla) antes de fijar tamaños y exportar.
