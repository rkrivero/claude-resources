# Módulo 11 — Relación de aspecto / banking to 45° (Cleveland)

El cerebro compara pendientes con máxima precisión cuando el ángulo medio de los
segmentos ronda los 45°. La forma del lienzo no debe ser un accidente.

```python
import numpy as np
import matplotlib.pyplot as plt

def aspecto_banking_45(x, y):
    """
    Calcula la relación de aspecto (alto/ancho de los datos) que lleva la
    pendiente media a ~45°. Devuelve figsize sugerido.
    """
    x = np.asarray(x, float); y = np.asarray(y, float)
    orden = np.argsort(x)
    dx = np.diff(x[orden]); dy = np.diff(y[orden])
    # pendiente media absoluta en unidades de dato
    pend = np.median(np.abs(dy[dx != 0] / dx[dx != 0]))
    rango_x = x.max() - x.min(); rango_y = y.max() - y.min()
    # factor que coloca la pendiente mediana en 45° (tan(45)=1)
    aspecto = (pend * rango_x) / rango_y if rango_y else 1.0
    ancho = 6.0
    alto = max(2.0, min(8.0, ancho * aspecto * (rango_y / rango_x)))
    print(f"Pendiente mediana={pend:.3g} → figsize≈({ancho:.1f}, {alto:.1f})")
    return (ancho, alto)

def lineplot_banked(df, x, y):
    import seaborn as sns
    fig, ax = plt.subplots(figsize=aspecto_banking_45(df[x], df[y]))
    sns.lineplot(data=df, x=x, y=y, ax=ax)
    ax.set_box_aspect(None)
    return fig
```

Regla: no aceptar el `figsize` por defecto para gráficos de tendencia clave;
ajustar para que las pendientes se lean a ~45°. No exagerar (lienzo alto y
estrecho dramatiza) ni aplanar (ancho y bajo banaliza) el efecto.
