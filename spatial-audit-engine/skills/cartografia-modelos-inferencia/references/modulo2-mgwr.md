# Módulo 2 — Parámetros locales y variación multiescala (mgwr)

```python
import numpy as np
from mgwr.gwr import GWR, MGWR
from mgwr.sel_bw import Sel_BW

def estandarizar_X(X):
    """Coeficientes estandarizados (Z): media 0, sd 1. Imprescindible para MGWR y
    para unificar la leyenda de los small multiples (todo en desviaciones estándar)."""
    return (X - X.mean(axis=0)) / X.std(axis=0)

def estimar_mgwr(coords, y, X, kernel="bisquare", fixed=False):
    """
    y:(n,1); X:(n,k) ESTANDARIZADAS. fixed=False => ancho de banda ADAPTATIVO (k-vecinos).
    La forma del kernel (gaussian/bisquare/tricube) influye <1%; lo decisivo es
    fijo vs. adaptativo.
    """
    y = y.reshape(-1, 1)
    sel = Sel_BW(coords, y, X, multi=True, kernel=kernel, fixed=fixed)
    bws = sel.search()
    res = MGWR(coords, y, X, sel, kernel=kernel, fixed=fixed).fit()
    return res, bws

def interpretar_bandwidths(bws, n, nombres):
    """Bandwidth alto (~n) => proceso global; bajo => proceso muy localizado."""
    for nombre, bw in zip(nombres, bws):
        escala = "GLOBAL (gradiente casi plano)" if bw > 0.8 * n else \
                 "LOCAL (alta variación)" if bw < 0.25 * n else "INTERMEDIA"
        print(f"{nombre}: bw={bw:.0f}/{n}  → {escala}")

def superficie_coef(gdf, res, idx, nombre):
    """Mapea β_i de una variable RESPETANDO su escala (no suavizar uniforme)."""
    gdf = gdf.copy()
    gdf[f"beta_{nombre}"] = res.params[:, idx]
    return gdf   # el render (divergente + máscara) va en módulos 3-4
```

Regla: PROHIBIR suavizar todas las variables por igual. Variables con bandwidth
bajo se representan con su variación local real; variables con bandwidth alto se
fijan con gradiente casi plano para no inventar fluctuaciones locales. Usar el t
crítico ajustado de `mgwr` (`res.critical_tval()`) en el Módulo 3.

## Auditoría de sobreajuste por ancho de banda (Fotheringham)

`Sel_BW` minimiza AICc; con datos ruidosos (scraping) puede elegir un bandwidth
diminuto (15–20 vecinos) y producir un mapa hiper-fragmentado que mapea RUIDO, no
heterogeneidad real. No aceptar el bandwidth automático sin auditarlo.

```python
def auditar_bandwidth(bw, n, min_vecinos_teorico=None):
    """Alerta si el bw es sospechosamente pequeño frente al tamaño muestral."""
    frac = bw / n
    if frac < 0.05 or (min_vecinos_teorico and bw < min_vecinos_teorico):
        print(f"ALERTA sobreajuste: bw={bw:.0f} (~{frac:.0%} de n). "
              f"El mapa puede mapear ruido. Considerar forzar un bw más amplio.")
    return frac

# Forzar manualmente un bandwidth de restricción (sacrifica AICc por coherencia):
from mgwr.gwr import GWR
def gwr_bw_fijo(coords, y, X, bw_fijo):
    return GWR(coords, y.reshape(-1,1), X, bw=bw_fijo, fixed=False).fit()
```

Regla: contrastar el bw óptimo con el conocimiento del territorio. Si el software
dice que el efecto cambia por completo cada dos cuadras, desconfiar y preferir un
bandwidth más amplio que capture la tendencia estructural del submercado.

## Fijo vs. adaptativo (Fotheringham)

La forma del kernel (gaussian/bisquare/tricube) cambia <1% el resultado. Lo
decisivo es el TIPO de ancho de banda:
- **Fijo** (radio en metros): en ciudades con topografía pronunciada o densidad irregular, 500 m
  pueden abarcar decenas de manzanas en el centro y ninguna en la periferia → sesgo
  geográfico. EVITAR salvo densidad homogénea.
- **Adaptativo** (k-vecinos, `fixed=False`): el radio se contrae en zonas densas
  y se expande en periferias dispersas; cada punto se calibra con la misma
  cantidad de información. PREFERIR por defecto.

## Coeficientes estandarizados (Z)

Estandarizar las X (`estandarizar_X`) antes de estimar deja todos los `β_i` en la
misma unidad (impacto por desviación estándar). Esto permite unificar una sola
leyenda divergente (ej. −3 a +3) en el panel de small multiples del Skill
`composicion-lienzo-cartografico` (modulo8) y comparar el peso real de cada
determinante de un vistazo.
