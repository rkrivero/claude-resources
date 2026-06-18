# Módulo 7 — Semiología gráfica de Bertin

Regla: la variable visual debe coincidir con el nivel de medición del dato.

| Nivel de dato | Variables visuales permitidas | Prohibidas |
|---------------|-------------------------------|------------|
| Nominal / categórico (tipologías momepy, uso de suelo) | Tono (hue), Forma | Luminosidad (value), Tamaño |
| Ordinal | Luminosidad, Tamaño, Tono ordenado | — |
| Cuantitativo (precio, densidad) | Luminosidad, Tamaño, paletas secuenciales/divergentes | Tono arbitrario sin orden |

```python
import matplotlib.pyplot as plt

def auditar_codificacion(nivel, variable_visual):
    """Bloquea combinaciones que mienten al ojo."""
    prohibidas = {"nominal": {"value", "luminosidad", "size", "tamaño"}}
    if nivel in prohibidas and variable_visual.lower() in prohibidas[nivel]:
        raise ValueError(
            f"Nominal con '{variable_visual}': el ojo lee jerarquía inexistente. "
            f"Usar tono (hue categórico) o forma.")
    return True

def mapa_categorico(gdf, col):
    """Categórico => paleta cualitativa (tono), NO secuencial."""
    return gdf.plot(column=col, categorical=True, cmap="tab10",
                    legend=True, edgecolor="0.7", linewidth=0.3)
```

Si se usa oscuridad para datos cualitativos, el lector asume que una categoría es
"superior". Reservar tamaño y luminosidad para variables cuantitativas ordenadas.
