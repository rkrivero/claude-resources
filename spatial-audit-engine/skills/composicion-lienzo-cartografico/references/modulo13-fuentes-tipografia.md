# Módulo 13 — Selección de fuentes y tipografía cartográfica (Brewer, Cap. 6-7)

## Regla de las dos fuentes

Usar **máximo dos familias tipográficas** por mapa:
- Una **sans-serif** para entidades culturales y administrativas (distritos, barrios, vías).
- Una **serif** para rasgos físicos e hidrográficos (ríos, quebradas, cotas).

PROHIBIR mezclar dos sans-serif distintas o dos serifs distintas en el mismo mapa.
PROHIBIR fuentes decorativas / display (Impact, Giddyup, Lobster) en cualquier etiqueta de mapa.

```python
import matplotlib.pyplot as plt
import matplotlib.font_manager as fm

# Familias open-source recomendadas —
# sans-serif (culturales): Fira Sans, Source Sans Pro, IBM Plex Sans
# serif     (físicos)    : Crimson Text, Linux Libertine O, EB Garamond
FUENTE_CULTURAL = "Fira Sans"  # ajustar según instalación
FUENTE_FISICA   = "Linux Libertine O"

def aplicar_fuentes_mapa(ax, textos_culturales, textos_fisicos):
    """Fuerza la convención tipográfica de Brewer en etiquetas ya trazadas."""
    for t in textos_culturales:
        t.set_family(FUENTE_CULTURAL)
    for t in textos_fisicos:
        t.set_family(FUENTE_FISICA)

def rcparams_fuentes():
    """
    Configura rcParams con la familia principal.
    Llamar ANTES de trazar cualquier texto.
    """
    plt.rcParams.update({
        "font.family": "sans-serif",
        "font.sans-serif": [FUENTE_CULTURAL, "DejaVu Sans"],  # fallback
        "font.serif":      [FUENTE_FISICA, "DejaVu Serif"],
    })
```

## Tamaño mínimo y x-height

| Contexto | Tamaño mínimo | Criterio |
|---|---|---|
| Impresión (tesis A4) | 6 pt | Menor valor legible |
| Pantalla (diapositiva) | 12 pt | Lectura a distancia proyector |
| Leyenda impresa | 7–9 pt | Perfil `modulo5-export.md` |

**X-height**: a igual tamaño en puntos, una fuente con x-height grande
(Fira Sans, Gill Sans) es más legible que una de x-height pequeña
(Centaur, Garamond PT) en cuerpos pequeños (≤8 pt). Preferir fuentes
de x-height generosa para etiquetas de 7–8 pt.

```python
def tamanio_segun_medio(medio="impreso"):
    """Devuelve tamaños de fuente por jerarquía y medio."""
    if medio == "impreso":
        return dict(titulo=11, subtitulo=9, etiqueta=7, meta=6.5)
    else:   # pantalla
        return dict(titulo=16, subtitulo=13, etiqueta=12, meta=11)
```

## Variantes tipográficas como variable visual semiótica

Bertin acepta variantes dentro de una misma familia como diferenciación:

```python
ESTILOS_JERQUIA = {
    "capital_distrito":  dict(fontsize=8,   fontweight="bold",   fontstyle="normal"),
    "barrio_urbano":     dict(fontsize=7,   fontweight="normal", fontstyle="normal"),
    "rasgo_hidrico":     dict(fontsize=7,   fontweight="normal", fontstyle="italic"),  # serif, cursiva
    "cotas_altura":      dict(fontsize=6.5, fontweight="light",  fontstyle="normal"),
}

def texto_jerarquico(ax, x, y, texto, tipo):
    return ax.text(x, y, texto, **ESTILOS_JERQUIA[tipo])
```

## Tracking para etiquetas de área (Brewer)

El **tracking** (espaciado entre caracteres) extiende un topónimo para
que cubra visualmente el polígono al que pertenece, sin agrandar el cuerpo.
Usar SOLO en etiquetas de área cuando el polígono es ancho (distrito extenso).

```python
def etiqueta_area_tracking(ax, x, y, texto, espaciado=0.15):
    """
    espaciado: fracción adicional del ancho de carácter.
    matplotlib usa letterspacing vía PathEffects o un workaround de expansión.
    Alternativa: insertar espacios delgados entre caracteres si la fuente no lo soporta.
    """
    texto_espaciado = (" " * int(espaciado * 10)).join(texto)
    return ax.text(x, y, texto_espaciado, ha="center", va="center",
                   fontsize=7, fontstyle="italic")
```

## Incrustación de fuentes en PDF

Ver `modulo5-export.md`: `pdf.fonttype=42` y `ps.fonttype=42` garantizan que
las fuentes queden incrustadas (TrueType embebido) en el PDF de la tesis.
Sin esta configuración el archivo depende de que el sistema del revisor tenga
instaladas las mismas fuentes.

## Checklist tipográfico antes de exportar

- [ ] ≤ 2 familias en el mapa
- [ ] Sin fuentes display/decorativas
- [ ] Etiquetas culturales en sans-serif, hidrográficas en serif+cursiva
- [ ] Tamaño mínimo ≥ 6 pt (impresión) / ≥ 12 pt (pantalla)
- [ ] `pdf.fonttype=42` activado
- [ ] Tracking solo en etiquetas de área extensa
