# Módulo 3 — Matriz de pesos espaciales W (libpysal)

```python
from libpysal.weights import Queen, Rook, KNN, DistanceBand
import numpy as np
import matplotlib.pyplot as plt

def construir_W(gdf, tipo_entidad):
    """Regla de negocio: contigüidad para polígonos, KNN/distancia para puntos."""
    if tipo_entidad == "field":           # polígonos continuos (manzanas)
        w = Queen.from_dataframe(gdf, use_index=True)   # Queen por defecto (esquinas+lados)
        metodo = "Queen"
    else:                                  # puntos dispersos / muy irregulares
        coords = np.c_[gdf.geometry.x, gdf.geometry.y]
        w = KNN.from_array(coords, k=8)    # garantiza que nadie quede aislado
        metodo = "KNN(k=8)"
    return w, metodo

def auditar_islas(w):
    """Buscar islas estadísticas y componentes desconectados."""
    if len(w.islands) > 0:
        print(f"ALERTA: {len(w.islands)} islas (peso 0): {w.islands[:10]}")
        print("→ Usar KNN/DistanceBand o revisar barreras antes de continuar.")
    n_comp = w.n_components if hasattr(w, "n_components") else None
    print(f"Componentes conectados: {n_comp}")
    return w.islands

def grafo_conectividad(gdf, w, ax=None):
    """Superpone aristas centroide-centroide sobre el mapa para validar barreras."""
    ax = gdf.plot(facecolor="none", edgecolor="0.7", linewidth=0.3) if ax is None else ax
    cents = gdf.geometry.centroid
    for i, vecinos in w.neighbors.items():
        x0, y0 = cents.iloc[i].x, cents.iloc[i].y
        for j in vecinos:
            x1, y1 = cents.iloc[j].x, cents.iloc[j].y
            ax.plot([x0, x1], [y0, y1], color="crimson", linewidth=0.4, alpha=0.6)
    ax.set_axis_off()
    return ax

# Estandarizar SIEMPRE por filas antes de usar
def estandarizar(w):
    w.transform = "r"   # row-standardized
    return w
```

Regla de validación visual: si una arista cruza un cerro empinado o un río sin
puente uniendo barrios desconectados en la realidad, la `W` está mal calibrada →
recortar la vecindad por accesibilidad real (p. ej. red vial, no distancia
euclídea).

Edición manual de enlaces artificiales (cuando una barrera real no debe conectar
dos unidades):

```python
def romper_enlace(w, i, j):
    """Elimina la arista i-j en ambos sentidos y reestandariza."""
    for a, b in ((i, j), (j, i)):
        if b in w.neighbors[a]:
            pos = w.neighbors[a].index(b)
            w.neighbors[a].pop(pos)
            w.weights[a].pop(pos)
    from libpysal.weights import W as Wcls
    w2 = Wcls(w.neighbors, w.weights)
    w2.transform = "r"
    return w2
```

Nota: si trabajas tejido urbano con `momepy`, su grafo de calles
(`momepy.gdf_to_nx`) puede dar una vecindad basada en conectividad vial real, más
fiel que la contigüidad pura cuando hay barreras topográficas.
