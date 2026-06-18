# Módulo 5 — Salida según medio y export

```python
import matplotlib as mpl

PERFIL_IMPRESO = {
    "legend_fontsize": 8,      # 7–9 pt
    "poly_linewidth": 0.3,     # 0.2–0.35 pt
    "poly_edgecolor": "0.7",   # gris/blanco, no negro
    "dpi": 300,                # mínimo ráster
    "formatos": ["pdf", "eps"],# vectorial sin pérdida
}
PERFIL_PANTALLA = {
    "legend_fontsize": 13,     # 12–14 pt
    "poly_linewidth": 0.9,     # ≥0.75 pt evita flickering
    "poly_edgecolor": "0.5",
    "dpi": 150,
    "formatos": ["png"],
}

def aplicar_perfil(perfil):
    mpl.rcParams.update({
        "legend.fontsize": perfil["legend_fontsize"],
        "savefig.dpi": perfil["dpi"],
        "pdf.fonttype": 42,    # fuentes editables/incrustadas en PDF
        "ps.fonttype": 42,
    })

def exportar(fig, nombre, perfil):
    for fmt in perfil["formatos"]:
        fig.savefig(f"{nombre}.{fmt}", dpi=perfil["dpi"],
                    bbox_inches="tight", facecolor="white")
        print(f"Exportado {nombre}.{fmt}")
```

Reglas: confirmar el medio ANTES de fijar tamaños. Impreso (tesis): fuentes 7–9
pt, bordes 0.2–0.35 pt en gris, PDF/EPS o ≥300 DPI, verificar CMYK. Pantalla:
fuentes 12–14 pt, bordes ≥0.75 pt, paletas de alto contraste para proyector.
Nota CMYK: matplotlib exporta en RGB; para impresión profesional convertir el
PDF a CMYK con Ghostscript/Scribus y revisar prueba de color.

## Escala gráfica vs. RF (Brewer, Cap. 4)

PREFERIR SIEMPRE la escala gráfica (barra) sobre el Representative Fraction (1:X).

- La barra **resiste** cualquier cambio de tamaño (zoom digital, fotocopia, impresión
  en formato distinto al diseñado). El RF se rompe: si el mapa se imprime al 80%,
  el RF "1:20 000" deja de ser cierto.
- En tesis digitales que se visualizan en pantallas de distintos tamaños, el RF es
  especialmente engañoso.

```python
# Ver agregar_barra_escala() en modulo1-jerarquia-layout.md
# No añadir el RF al lado de la barra: es redundante y crea el error
# de que "ambos son siempre correctos" cuando solo la barra lo es.
```

## Norte arrow (Brewer, Cap. 4)

El norte arrow es REDUNDANTE si:
- El mapa tiene una cuadrícula UTM o de lat/lon etiquetada.
- El mapa está orientado al norte convencional (la gran mayoría de mapas).

Solo es OBLIGATORIO cuando:
- El mapa tiene una orientación no convencional (por ejemplo, rotado para ajustar
  un corredor alargado en diagonal).
- El mapa es de navegación donde la orientación es ambigua para el usuario.

Símbolo correcto: línea vertical simple con "N" en la punta. Prohibir rosas de
los vientos complejas — son decoración, no información.
