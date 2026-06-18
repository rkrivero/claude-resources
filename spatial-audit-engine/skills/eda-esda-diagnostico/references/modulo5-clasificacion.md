# Módulo 5 — Clasificación estadística (mapclassify) + ADCM

```python
import mapclassify as mc
import numpy as np

def elegir_clasificador(y, k=6, cola_pesada=False, posicion_relativa=False):
    """Reglas de decisión de clasificador."""
    if cola_pesada:
        clf = mc.HeadTailBreaks(y)          # ley de potencias / cola extrema
        nombre = "HeadTailBreaks"
    elif posicion_relativa:
        clf = mc.Quantiles(y, k=k)          # 20% más caro vs más barato
        nombre = "Quantiles"
        print("ADVERTENCIA: cuantiles agrupa extremos distantes y rompe valores "
              "similares en el centro de la distribución.")
    else:
        clf = mc.FisherJenks(y, k=k)        # OBLIGATORIO por defecto (saltos bruscos)
        nombre = "FisherJenks"
    return clf, nombre

def adcm_optimo(y, ks=(5, 6, 7)):
    """Desviación Absoluta alrededor de la Mediana de Clase. Elige k donde el ajuste se estabiliza."""
    res = {}
    for k in ks:
        clf = mc.FisherJenks(y, k=k)
        res[k] = clf.adcm
        print(f"k={k}: ADCM={clf.adcm:.2f}  (gof={1 - clf.adcm/ (np.abs(y-np.median(y)).sum()):.3f})")
    # regla: detener cuando la mejora marginal de ADCM se aplana; nunca k>7
    mejor = min(ks, key=lambda k: res[k])
    print(f"Sugerido: k={mejor} (no exceder 7 clases; el ojo no las diferencia).")
    return res
```

Reglas: Fisher-Jenks por defecto; Quantiles solo con advertencia explícita;
Head-Tail Breaks si la cola es pesada. Validar el número de clases con ADCM para
k∈{5,6,7} y elegir donde la ganancia se estabiliza. Las paletas y el render se
delegan al skill `composicion-lienzo-cartografico`.
