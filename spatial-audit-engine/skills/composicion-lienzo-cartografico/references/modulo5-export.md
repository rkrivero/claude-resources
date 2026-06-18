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
