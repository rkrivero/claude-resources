# Módulo 3 — Checklist y guía de redacción de la sección metodológica

## Orden lógico (research proposal)

1. **Sección de datos** → fig. 1 (cobertura) + fig. 2 (EDA). "Los 380 datos están
   limpios y cubren el territorio".
2. **Modelado del espacio** → fig. 3 (grafo de conectividad). Define formalmente la
   matriz `W`.
3. **Planteamiento de hipótesis espacial** → fig. 4 (Moran scatterplot). El
   diagnóstico que rompe la estadística clásica y abre las hipótesis de
   heterogeneidad local.
4. **Formulación del modelo** → ecuaciones OLS global + estructura MGWR local con
   anchos de banda adaptativos (justificados por densidad variable de la muestra).

## Checklist de aprobación

- [ ] Mapa de cobertura muestra distribución, no concentración en un barrio.
- [ ] EDA evidencia asimetría → se propone transformación log/Box-Cox.
- [ ] Grafo de `W` sin islas; criterio (KNN k=?) justificado por la topografía.
- [ ] Moran global significativo (p<0.05) reportado con permutaciones.
- [ ] Ecuaciones OLS y MGWR escritas formalmente.
- [ ] Limitaciones de escala declaradas (no inferir lotes desde agregados).

## Plantillas de redacción (rellenar)

**Datos:** "Se georreferenciaron ___ unidades de observación en el área de estudio
(CRS: [EPSG del área]). La Figura 1 muestra su cobertura sobre el continuo
urbano; la densidad KDE confirma representación desde el centro hasta
las zonas periféricas del área de estudio."

**Justificación espacial:** "El Índice de Moran global (I = ___, p = ___, 999
permutaciones) indica autocorrelación espacial positiva y significativa en la
variable dependiente. Por tanto, un OLS clásico viola el supuesto de independencia
de las observaciones, lo que motiva un enfoque de econometría espacial."

**Modelo:** "Se estimará primero un OLS global como línea base. Ante la
heterogeneidad espacial esperada, se calibrará un MGWR con anchos de banda
adaptativos (k-vecinos), que ajusta la escala de cada determinante a la densidad
local variable de la muestra."

Para el documento formal (APA 7, Word/PDF), derivar a las skills de redacción
académica disponibles (academic-paper) o a la skill `docx`.
