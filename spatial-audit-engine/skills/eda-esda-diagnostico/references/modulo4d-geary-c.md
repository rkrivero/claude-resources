# Módulo 4d — Índice de Geary's c

Complemento de Moran's I para medir autocorrelación espacial global. Mientras Moran
compara cada valor con la media global (covarianza espacial), Geary compara valores
de pares de vecinos directamente (varianza de diferencias locales), lo que lo hace
más sensible a diferencias entre unidades adyacentes que a la estructura de clusters globales.

## Rango e interpretación

| Valor de c | Interpretación |
|------------|---------------|
| c ≈ 0      | Autocorrelación positiva perfecta (similar a I ≈ +1) |
| c ≈ 1      | Aleatoriedad espacial (H₀) |
| c ≈ 2      | Autocorrelación negativa perfecta (tablero de ajedrez) |

Regla de lectura: `c < 1` → clustering; `c > 1` → dispersión.
Nota: Geary y Moran no son inversamente proporcionales de forma exacta salvo
para W de conectividad simple.

## Código

```python
from esda.geary import Geary
import libpysal

def calcular_geary(y, w, permutations: int = 999, seed: int = 42):
    """
    y: array-like con la variable de interés
    w: libpysal.weights.W estandarizada por filas
    """
    gearys = Geary(y, w, permutations=permutations, seed=seed)
    print(f"Geary's c = {gearys.C:.4f}")
    print(f"p-valor (permutaciones) = {gearys.p_sim:.4f}")
    print(f"z-score = {gearys.z_sim:.4f}")
    return gearys
```

## Cuándo usar Geary en lugar de (o además de) Moran

| Situación | Preferir |
|-----------|----------|
| Detectar clustering global | Moran's I (más potente para clústeres grandes) |
| Detectar heterogeneidades locales entre vecinos | Geary's c (más sensible a variación local) |
| Confirmar robustez del resultado | Reportar ambos — si coinciden, el patrón es robusto |
| Distribuciones con outliers extremos | Geary's c (menos influenciado por valores globales) |

## Relación con Moran

Para matrices de peso binario simétricas:

```
I ≈ 1 − c/2   (aproximación; la igualdad exacta depende de W)
```

Si Moran indica clustering pero Geary no (o viceversa), investigar heterocedasticidad
o anisotropía espacial.

## Reporte en texto (APA 7)

```
El índice de Geary (c = 0.62, z = −4.83, p < .001) confirmó la presencia
de autocorrelación espacial positiva, consistente con el Índice de Moran
global (I = 0.34, z = 8.21, p < .001, 999 permutaciones).
```

## Limitaciones

- No tiene análogo local (no hay LISA de Geary disponible en esda de forma estándar).
- Menos usado en la literatura empírica que Moran; si se reporta sin Moran, puede
  generar confusión en revisores no especialistas.
- Como I, asume estacionariedad de segundo orden (la estructura de autocorrelación
  es la misma en todo el territorio).
