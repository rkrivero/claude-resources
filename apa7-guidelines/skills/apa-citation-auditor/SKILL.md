---
name: apa-citation-auditor
description: >
  Usa este skill cuando el usuario pida verificar, corregir o formatear
  citas APA 7. Disparadores: "revisa mis citas", "verifica las referencias",
  "corrige el formato APA", "chequea el bib", "esta cita está bien en APA 7",
  "falta el DOI", "et al. correcto", "cómo cito este artículo/libro/capítulo",
  "referencia mal formada", "audita la bibliografía". Cubre citas en texto
  (parentéticas y narrativas), lista de referencias (4 elementos: autor, fecha,
  título, fuente) y ejemplos de los tipos más frecuentes en investigación
  cuantitativa y espacial: artículos de revista, libros, capítulos, informes,
  tesis, páginas web, datasets espaciales, software y paquetes Python/R.
metadata:
  version: "0.1.0"
  author: "cusco-estructura"
  source: "APA Publication Manual 7th ed., Caps. 8-10"
---

# APA Citation Auditor — Skill de auditoría de citas y referencias APA 7

Opera como auditor implacable, no como corrector pasivo. Detecta errores,
los clasifica por tipo y entrega una versión corregida con la regla violada
entre corchetes. No preguntar si el usuario quiere correcciones — aplicarlas.

## Principio operativo

Ante cualquier cita o referencia: (1) identificar el tipo de fuente,
(2) aplicar la plantilla de ese tipo desde `references/tipos-referencia.md`,
(3) verificar la cita en texto correspondiente desde `references/citas-texto.md`,
(4) emitir diagnóstico con error + regla + versión corregida.

Si hay acceso a Zotero MCP (`mcp__zotero-mcp__*`), cruzar la key citada
contra la biblioteca para verificar que los metadatos coinciden.

---

## Módulo 1 — Auditoría de citas en texto (Cap. 8)

### Reglas de oro (no negociables)

**Formato básico**
- Parentética: `(Apellido, año)` — `(Rodríguez, 2022)`
- Narrativa: `Rodríguez (2022) encontró…`
- Cita directa: añadir número de página → `(Rodríguez, 2022, p. 45)` o
  `(Rodríguez, 2022, pp. 45–46)` para rango

**Número de autores (regla et al.)**
| Autores | Primera cita | Citas siguientes |
|---------|-------------|-----------------|
| 1–2     | ambos apellidos | ambos apellidos |
| 3+      | primer apellido + *et al.* | igual |

*Excepción*: si "Primer Autor et al., año" es idéntico a otra referencia,
añadir tantos apellidos adicionales como sea necesario para desambiguar.

**Dos obras del mismo autor y año**
Añadir letra minúscula: `(Torres, 2021a)`, `(Torres, 2021b)`. El orden
de las letras sigue el orden alfabético del título.

**Obras sin fecha** → `s.f.`: `(ONU-Hábitat, s.f.)`

**Citas de múltiples fuentes en un paréntesis**
Ordenar alfabéticamente por primer apellido, separadas por punto y coma:
`(Flores, 2019; García, 2021; Torres, 2020)`

**Errores frecuentes a marcar**
- [ET-AL-INCORRECTO] Usar et al. con solo 2 autores
- [FECHA-FALTANTE] Cita sin año
- [PAGINA-DIRECTA] Cita directa sin número de página
- [ORDEN-MULTIPLES] Múltiples fuentes no ordenadas alfabéticamente
- [NARRATIVA-MAL] "Según Flores et al. (2019, p. 23) afirma que…" — redundante
  el "que" después de cita narrativa con verbo
- [AMBIGUEDAD-ET-AL] Dos referencias colapsan al mismo "Autor et al., año"

---

## Módulo 2 — Auditoría de la lista de referencias (Cap. 9)

### Estructura de los 4 elementos

```
Autor. (Fecha). Título. Fuente.
```

**Elemento 1: Autor**
- Formato: `Apellido, I.` (inicial del nombre)
- Múltiples autores: separar con coma + `&` antes del último
- Hasta 20 autores: listar todos
- 21+ autores: primeros 19, `…` (elipsis), último autor
- Sin autor individual → usar nombre de organización como autor
- Sin autor ni organización → el título sube a posición de autor

**Elemento 2: Fecha**
- `(Año).` para la mayoría
- `(Año, Mes Día).` para entradas de blog, noticias, redes sociales
- `(s.f.).` si no hay fecha
- `(Año-a).` / `(Año-b).` para dos obras del mismo autor y año

**Elemento 3: Título**
- *Artículos/capítulos*: sin cursiva, sin comillas → texto plano
- *Libros/informes/tesis*: cursiva
- Solo la primera palabra y nombres propios en mayúscula (sentence case)

**Elemento 4: Fuente (para artículos de revista)**
- *Nombre de la revista* en cursiva (title case: mayúscula en palabras principales)
- `,` *volumen*(número), páginas.
- Número de issue siempre incluido en APA 7
- DOI como hyperlink: `https://doi.org/xxxxx` — sin la etiqueta "DOI:"
- URL solo si no hay DOI; "Recuperado de" únicamente si se necesita fecha de recuperación

**Errores frecuentes**
- [ISSUE-FALTANTE] Artículo sin número de issue
- [DOI-ETIQUETA] Usar "DOI:" antes del link
- [URL-SIN-DOI] URL cuando existe DOI
- [LUGAR-EDITORIAL] Incluir ciudad editorial en libro (APA 7 lo eliminó)
- [SENTENCE-CASE] Mayúscula en todas las palabras del título del artículo
- [ELIPSIS-AUTORES] No usar `…` con ≤20 autores
- [RECUPERADO-DE] Añadir "Recuperado de" cuando no se necesita fecha
- [ORDEN-LISTA] Referencias no en orden alfabético por apellido

---

## Módulo 3 — Salida del diagnóstico

Para cada error, emitir en este formato:

```
[TIPO-ERROR] Texto original
→ Versión corregida
✦ Regla (APA 7, §X.XX): descripción de la regla
```

Al final: conteo de errores por tipo y una sección "Referencias corregidas"
con la lista completa en orden alfabético y formato APA 7 correcto.

Si el usuario pide solo verificar una referencia específica, aplicar los
módulos 1–2 solo a esa referencia y entregar diagnóstico + versión corregida.

---

## Módulo 4 — Integración con Zotero (cuando disponible)

Si `mcp__zotero-mcp__search_library` está disponible:
1. Extraer la clave o el título de la referencia
2. Buscar en la biblioteca Zotero para obtener metadatos autoritativos
3. Comparar campos: autor, año, título, journal, volumen, número, páginas, DOI
4. Reportar discrepancias entre lo citado y lo que está en Zotero

---

## Módulo 5 — Árbol de decisión de tipo de fuente

Ante una referencia de tipo incierto, aplicar el árbol completo en
`references/casos-especiales.md`. Tipos de mayor ambigüedad en investigación
urbana:

| Situación ambigua | Resolución |
|-------------------|-----------|
| ¿Capítulo en libro o artículo en actas? | Actas = editor distinto, congreso en el título → capítulo en libro editado con congreso como "editorial" |
| ¿Informe institucional o libro? | Informe = emitido por organización sin ISBN comercial → informe con institución como autor |
| ¿Dataset o software? | Dataset = archivo de datos descargable → `[Conjunto de datos]`; Software = ejecutable → `[Software]` |
| ¿Preprint o artículo publicado? | Preprint = sin volumen/número/páginas, en servidor tipo arXiv/OSF → referencia de preprint |
| ¿Tesis o capítulo de libro? | Tesis = grado académico explícito, universidad como depositaria |

---

## Módulo 6 — Verificación de coherencia cita ↔ referencia

Al auditar un texto completo, hacer correspondencia bidireccional:

**A → B (de citas al texto, a la lista):**
Para cada `(Autor, Año)` o `Autor (Año)` encontrado en el texto:
1. Buscar entrada en la lista de referencias con ese apellido y año
2. Verificar que el nombre completo y el año coincidan exactamente
3. Si hay `et al.` → verificar que la referencia tiene ≥3 autores

**B → A (de la lista, a las citas en texto):**
Para cada entrada en la lista de referencias:
1. Verificar que aparece al menos una cita en texto
2. Identificar referencias huérfanas (en lista pero no citadas)
3. Identificar citas huérfanas (en texto pero no en lista)

**Errores específicos de tesis en español:**
- [IDIOMA-ET-AL] Usar "y cols." o "y colaboradores" en lugar de *et al.*
- [FECHA-ES] Escribir `(García, año 2020)` → debe ser `(García, 2020)`
- [VERBO-EXTRA] `"Según indica García (2020)…"` → "Según" + narrativa es redundante: usar uno o el otro
- [IDEM-IBID] Usar "ídem" o "ibíd." — prohibidos en APA 7, repetir la cita completa

Cargar referencias: `references/citas-texto.md`, `references/lista-referencias.md`,
`references/tipos-referencia.md`, `references/casos-especiales.md`
