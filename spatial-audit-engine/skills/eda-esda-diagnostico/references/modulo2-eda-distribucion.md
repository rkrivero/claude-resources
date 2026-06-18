# Módulo 2 — Perfilado estadístico no espacial (EDA)

```python
import numpy as np
from scipy import stats
import seaborn as sns
import matplotlib.pyplot as plt

def diagnostico_distribucion(serie):
    """Asimetría/colas. Devuelve recomendación de transformación."""
    s = serie.dropna()
    skew = stats.skew(s)
    kurt = stats.kurtosis(s)
    jb_stat, jb_p = stats.jarque_bera(s)
    print(f"skew={skew:.2f}  kurtosis={kurt:.2f}  Jarque-Bera p={jb_p:.4f}")

    rec = "ninguna"
    if abs(skew) > 1 or jb_p < 0.05:
        # sesgo fuerte: el OLS global perderá eficiencia
        if (s > 0).all():
            rec = "log"   # sesgo a la derecha y positivos
        else:
            rec = "box-cox (requiere valores positivos)"
    print(f"Recomendación de transformación: {rec}")
    return {"skew": skew, "kurtosis": kurt, "jb_p": jb_p, "transform": rec}

def aplicar_boxcox(serie):
    """Devuelve serie transformada y lambda para heredar al Skill 4."""
    x, lam = stats.boxcox(serie.dropna().values)
    print(f"Box-Cox lambda={lam:.3f}")
    return x, lam

def jointplot_densidad(gdf, x, y):
    sns.jointplot(data=gdf, x=x, y=y, kind="hex", marginal_kws=dict(bins=40))
    plt.tight_layout()

def box_condicional(gdf, cat, val):
    """BoxPlot ordenado por mediana → revela heterocedasticidad entre submercados."""
    orden = gdf.groupby(cat)[val].median().sort_values().index
    sns.boxplot(data=gdf, x=cat, y=val, order=orden)
    plt.xticks(rotation=45, ha="right")
    plt.tight_layout()

def dispersión_antisobreploteo(gdf, x, y, umbral=2000):
    """
    Con miles de transacciones (scraping), el scatter sólido es un gráfico ciego:
    no distingue 10 de 5000 puntos encimados. Sobre el umbral, usar hexbin o KDE-2D.
    """
    n = len(gdf)
    if n <= umbral:
        return sns.scatterplot(data=gdf, x=x, y=y, s=10, alpha=0.5)
    # hexbinning: color = conteo exacto por celda
    ax = plt.gca()
    hb = ax.hexbin(gdf[x], gdf[y], gridsize=40, cmap="viridis", mincnt=1)
    plt.colorbar(hb, ax=ax, label="n observaciones")
    # alternativa: contornos KDE-2D para aislar picos de acumulación
    # sns.kdeplot(data=gdf, x=x, y=y, levels=8, fill=True, cmap="mako")
    return ax
```

Reglas: skewness alto + Jarque-Bera significativo ⇒ transformar antes de mapear o
modelar. Guardar `lambda` de Box-Cox para el Skill 4. Los `boxplot`/`violinplot`
condicionales SIEMPRE ordenados por la mediana del precio.

Atajo práctico: si la variable tiene ceros o valores muy pequeños, usar
`np.log1p` (log(1+x)) en lugar de `np.log` para estabilizar varianza sin perder
observaciones. Activar una bandera interna `transform=log1p` que el Skill 4
hereda. Nota: las variables morfológicas de `momepy` (compacidad, orientación,
áreas) suelen entrar como determinantes continuos del precio — perfilarlas aquí
también.
