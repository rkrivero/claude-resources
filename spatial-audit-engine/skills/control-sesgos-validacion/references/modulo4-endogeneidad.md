# Compuerta 4 — Endogeneidad espacial y variable omitida (Anselin)

¿El precio es alto por contagio real de vecinos (rezago) o por una variable
omitida compartida por el barrio (prestigio, seguridad, estética)? Si se omite, el
rezago la absorbe y exagera el peso de la vecindad.

## Paso 1 — exprimir morfología con momepy antes de tocar SAR

```python
import momepy

def variables_morfologicas(gdf_manzanas, calles_nx=None):
    """Genera determinantes geométricos para reducir la variable omitida."""
    g = gdf_manzanas.copy()
    g["compacidad"] = momepy.CircularCompactness(g).series
    g["orientacion"] = momepy.Orientation(g).series
    g["elongacion"] = momepy.Elongation(g).series
    # con grafo de calles: conectividad, densidad de cruces, meshedness
    # nodes, edges = momepy.nx_to_gdf(calles_nx)
    return g
```

## Paso 2 — decidir rezago vs. error con LM robustos

```python
from spreg import OLS

def decidir_lag_vs_error(y, X, w):
    """Tras añadir morfología, si Moran de residuos persiste, los LM robustos deciden."""
    w.transform = "r"
    m = OLS(y, X, w=w, spat_diag=True, moran=True)
    rlm_lag = m.rlm_lag[1]      # p-valor robusto LM-lag
    rlm_err = m.rlm_error[1]    # p-valor robusto LM-error
    if rlm_lag < 0.05 and rlm_err >= 0.05:
        return "SAR (rezago): interacción real de mercado"
    if rlm_err < 0.05 and rlm_lag >= 0.05:
        return "SEM (error): estructura omitida como ruido, protege coeficientes"
    if rlm_lag < 0.05 and rlm_err < 0.05:
        return "Ambos: revisar SDM/SAC o seguir buscando variable omitida"
    return "Ni lag ni error robustos: OLS con morfología puede bastar"
```

Regla: SEM es preferible cuando la estructura espacial faltante debe tratarse
como ruido no medido (protege la limpieza de los coeficientes principales); SAR
cuando hay un mecanismo real de contagio de precios. Conecta con el árbol LM del
skill `econometria-hedonica-espacial` (modulo2).
