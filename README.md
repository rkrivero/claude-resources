# claude-resources

Marketplace personal de [Claude Code](https://claude.com/claude-code) con los
plugins y skills que he creado. Pensado para usarse en cualquier proyecto y en
Cowork sin tener que cargar el repositorio de la tesis.

## Plugins

### `apa7-tesis` — calidad APA 7 y redacción académica
Motor de calidad APA 7 para tesis de arquitectura. Cuatro skills:

- **apa-citation-auditor** — auditoría de citas en texto y lista de referencias (Caps. 8–10).
- **jars-methodology-checker** — verificación de la metodología contra JARS-Quant (Cap. 3), calibrado para modelos hedónicos espaciales.
- **scholarly-writing-coach** — coaching de prosa académica según los Caps. 4 y 6 del Manual APA 7.
- **spanish-academic-style** — diagnóstico de vicios del español académico (sintaxis, economía léxica, gramática, lenguaje libre de sesgos) más allá de APA 7.

### `spatial-audit-engine` — auditoría de análisis espacial urbano
Motor de auditoría científica para análisis espacial. Siete skills:

- **eda-esda-diagnostico** — EDA/ESDA e indicadores locales.
- **econometria-hedonica-espacial** — modelos hedónicos espaciales (OLS, SAR, SEM, SLX, GWR/MGWR).
- **cartografia-modelos-inferencia** — cartografía de coeficientes e inferencia espacial.
- **composicion-lienzo-cartografico** — composición y exportación de figuras de mapa.
- **control-sesgos-validacion** — control de sesgos y validación.
- **entorno-pipeline-reproducible** — entorno y pipeline reproducible.
- **propuesta-viabilidad-espacial** — viabilidad del enfoque espacial para el plan de tesis.

## Uso

Añade el marketplace una sola vez:

```
/plugin marketplace add rkrivero/claude-resources
```

Luego instala los plugins que quieras:

```
/plugin install apa7-tesis@claude-resources
/plugin install spatial-audit-engine@claude-resources
```

Para actualizar tras un cambio: haz `git push` aquí y luego `/plugin update <nombre>`.
