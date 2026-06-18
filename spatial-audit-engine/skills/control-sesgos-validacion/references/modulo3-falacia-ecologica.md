# Compuerta 3 — Falacia ecológica y consistencia de escala

No inferir el individuo desde el agregado (Robinson, 1950). El espacio
intraurbano es heterogéneo: un distrito "barato" puede contener islas de alto
valor.

## Checklist de coherencia de escala

```python
def auditar_escala(unidad_datos, nivel_conclusiones):
    """
    unidad_datos: 'punto' | 'manzana' | 'distrito'
    nivel_conclusiones: 'lote' | 'manzana' | 'distrito'
    Bloquea conclusiones a una escala más fina que los datos.
    """
    rango = {"punto": 0, "lote": 0, "manzana": 1, "distrito": 2}
    if rango[nivel_conclusiones] < rango.get(unidad_datos, 99):
        raise ValueError(
            f"Falacia ecológica: datos a nivel '{unidad_datos}' no sostienen "
            f"conclusiones a nivel '{nivel_conclusiones}'. Bajar la escala del "
            f"modelo a micro-datos o limitar las conclusiones.")
    return True
```

Reglas:
- Conclusiones sobre decisiones de compradores / valor de predios ⇒ modelo a nivel
  de PUNTOS.
- Datos agregados por manzana (restricción censal) ⇒ conclusiones SOLO sobre
  manzanas.
- Dejar la limitación explícita en el texto de la tesis.
- Vincular con el diagnóstico MAUP (`eda-esda-diagnostico`, modulo1b): el agregado
  ya introduce efecto de escala/zonificación.
```
