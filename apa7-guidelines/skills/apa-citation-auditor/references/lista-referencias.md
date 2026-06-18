# Lista de referencias — APA 7, Cap. 9

## Principios generales

- Cada cita en texto debe tener entrada en la lista y viceversa
- Orden: alfabético por primer apellido del autor
- Si un autor tiene entradas sin año y con año: sin año primero
- Sangría francesa: primera línea al margen, líneas siguientes a 1.27 cm
- Doble espaciado dentro y entre entradas (estándar APA)

## Orden cuando hay empates

| Situación | Orden |
|-----------|-------|
| Mismo apellido, diferente inicial | Alfabético por inicial |
| Mismo autor, diferente año | Cronológico (más antiguo primero) |
| Mismo autor y año | Alfabético por título + letra (a, b, c…) |
| Autor + obra sin autor | Autor primero |

## Autores con más de 20

Listar los primeros 19, luego `…` (elipsis U+2026), luego el último autor:
```
Álvarez, A., Bravo, B., Campos, C., … Zamora, Z.
```

## Organizaciones como autoras

```
Ministerio de Vivienda, Construcción y Saneamiento. (2021). Título…
```

## Sin autor

El título sube a posición de autor y se ordena por la primera palabra
significativa del título (ignorar artículos: el, la, los, the, a, an).

## DOI y URL

- DOI siempre como hyperlink: `https://doi.org/10.xxxx/xxxxx`
- Sin la etiqueta "DOI:"
- URL solo si no existe DOI
- "Recuperado de" solo si se necesita fecha de recuperación (contenido
  que cambia sin historial de versiones: wikis, páginas sin fecha fija)
- Fecha de recuperación cuando el contenido puede cambiar:
  `Recuperado el 12 de mayo de 2025, de https://…`

## Sentence case para títulos

Solo mayúscula en: primera palabra, primera palabra después de `:`,
nombres propios, acrónimos.

```
Correcto:  Análisis de autocorrelación espacial en entornos urbanos
Incorrecto: Análisis de Autocorrelación Espacial en Entornos Urbanos
```

Revistas y libros mantienen title case en el *nombre* de la revista/libro:
`Revista Latinoamericana de Estudios Urbanos`

---

## Elementos faltantes — qué hacer (APA Tabla 9.1)

| Elemento faltante | Acción |
|-------------------|--------|
| Sin autor | El título sube a posición de autor |
| Sin fecha | `(s.f.)` |
| Sin título (obra visual/untitled) | Descripción en `[corchetes]` en su lugar |
| Sin fuente / no recuperable | Solo citar si realmente existe y es recuperable; si no, comunicación personal |
| Autor = mismo que la fuente (web institucional) | Omitir la fuente (no repetir) |

---

## Detalles de formato de autor (APA §9.8)

- Nombre con guion: `Xu, A.-J.` (guion conservado)
- Autor de un solo nombre (Platón, Sukarno): solo el nombre sin coma
- Autor con seudónimo de red social: `@usuario` entre corchetes si aplica
- Autores con sufijo (Jr., III): `García Pérez, R., Jr.`
- "con" como colaborador: `Rowling, J. K. (con Whisp, K.)` — en paréntesis

---

## DOI y URL — reglas críticas (APA §9.35–9.36)

- Formato: `https://doi.org/10.xxxx/xxxxx` — **sin punto al final** del DOI o URL
- Sin etiqueta "DOI:" ni "Disponible en:"
- URL solo si no hay DOI
- ShortDOI aceptable: `https://doi.org/g7j`
- URL acortada aceptable cuando la URL completa es muy larga

**Fecha de recuperación (§9.16):** incluir solo para fuentes inherentemente
inestables y no archivadas: perfiles de Twitter/X, entradas de diccionario en
línea en cambio continuo, Google Maps, wikis sin historial de versiones.

NO incluir fecha de recuperación para: artículos de revista en línea, páginas
web institucionales con URL estable, repositorios con DOI.

---

## Nombre del editor — reglas APA 7 (§9.29)

- **Sin ciudad/país** — el lugar editorial fue eliminado en APA 7
- Omitir: `Inc.`, `Ltd.`, `LLC`, `S.A.`, `Editorial`, `Publishers`
- Usar el nombre del sello (imprint), no la empresa matriz:
  `Springer` (no `Springer International Publishing AG`)
- Si el autor y el editor son la misma organización, omitir el editor

---

## Obras traducidas (APA §9.39)

Dar crédito al traductor + año de traducción y año del original:

```
Jacobs, J. (2011). Muerte y vida de las grandes ciudades (A. Useros, Trad.).
    Capitán Swing. (Obra original publicada en 1961)
```

Cita en texto: `(Jacobs, 1961/2011)`

## Obras en otro idioma (APA §9.38)

Incluir traducción del título entre corchetes en el idioma del documento:

```
Fujita, M., Krugman, P., & Venables, A. J. (2000). The spatial economy:
    Cities, regions and international trade [La economía espacial: ciudades,
    regiones y comercio internacional]. MIT Press.
```

---

## Artículos en prensa / preprints

Artículo en prensa (aceptado, sin volumen/número/páginas aún):
```
Autor, A. A. (en prensa). Título del artículo. Nombre de la Revista.
    https://doi.org/xxxxx
```

Preprint (sin revisión de pares):
```
Autor, A. A. (Año). Título. Nombre del servidor de preprints.
    https://doi.org/xxxxx
```

---

## Meta-análisis — marcado con asterisco

Las fuentes incluidas en un meta-análisis se marcan con `*` al inicio:
```
* Anselin, L. (1995). Local indicators of spatial association — LISA.
    Geographical Analysis, 27(2), 93–115.
    https://doi.org/10.1111/j.1538-4632.1995.tb00338.x
```

Una nota al inicio de la lista de referencias declara:
`Nota. Los estudios incluidos en el meta-análisis están marcados con asterisco.`

---

## Orden cuando hay apellidos compuestos o con partículas

- Apellido con partícula sin mayúscula que forma parte del apellido al indexar:
  `De la Torre, R.` → ordenar por D
- Con partícula separada que no forma parte al indexar:
  `van der Berg, J.` → ordenar por B (pero escribir completo)
- Nombres hispanos con apellido paterno + materno: usar ambos si el autor los usa,
  o solo el paterno si solo ese aparece en la publicación

---

## Indicadores de tipo de fuente entre corchetes

Incluir solo cuando el tipo de fuente no es obvio por el resto de la referencia:

| Tipo | Indicador |
|------|-----------|
| Dataset / shapefile | [Conjunto de datos] |
| Software | [Software] |
| Tesis de licenciatura | [Tesis de licenciatura, Universidad X] |
| Tesis de maestría | [Tesis de maestría, Universidad X] |
| Mapa | [Mapa] |
| Videoclip | [Video] |
| Podcast | [Podcast de audio] |
| Presentación de congreso (no publicada) | [Presentación en congreso] |

No usar [Artículo de revista] ni [Libro] — estos tipos son evidentes.
