# Tells de IA en castellano — tabla de referencia

Léxico, frases y calcos que aparecen con frecuencia desproporcionada en texto en
español generado por LLM. **No** son errores gramaticales (para eso está
`corrector-prosa-castellana`); son marcadores estadísticos de origen-IA. Marcar
por **clusters**, no por aparición aislada.

## Índice
1. Léxico de alta frecuencia
2. Verbos de evitación de cópula
3. Frases-marco y arranques
4. Señalización y meta-comentario
5. Cierres y trascendencia
6. Calcos del inglés (falsos amigos de estilo)
7. Artefactos de chatbot

---

## 1. Léxico de alta frecuencia `[IA-LEXICO]`

| Palabra/locución IA | Alternativa / acción |
|---|---|
| cabe destacar / cabe mencionar / cabe resaltar | eliminar y afirmar directamente |
| es importante señalar / destacar / mencionar | eliminar; ir al dato |
| en el marco de / en el ámbito de | en / dentro de / para |
| abordar (por *tratar*, *estudiar*) | tratar / estudiar / analizar |
| profundizar / ahondar en | estudiar / detallar |
| de la mano de | con / mediante |
| a su vez (como muletilla) | y / además (o eliminar) |
| por ende / así pues (apilados) | por lo tanto (uno solo, o ninguno) |
| en la actualidad / hoy en día (arranque) | hoy / actualmente, o eliminar |
| sin duda / indudablemente | eliminar (afirmación directa) |
| en constante evolución / panorama cambiante | describir el cambio concreto |
| enriquecedor / fascinante / apasionante | eliminar o concretar por qué |
| rico/a (figurado: rica tradición) | eliminar el adjetivo valorativo |
| vibrante | eliminar |
| robusto (calco de *robust*) | sólido / fiable / bien especificado |
| holístico / integral (decorativo) | eliminar o concretar el alcance |
| sinergia | colaboración / efecto conjunto (si aplica) |
| paradigma (decorativo) | enfoque / modelo |
| crisol / mosaico / tapiz / entramado | eliminar la metáfora; describir |

## 2. Verbos de evitación de cópula `[IA-COPULA]`

| Evitación | Forma directa |
|---|---|
| constituye | es |
| representa | es |
| supone | es / implica |
| conforma | forma / es |
| se configura como | es |
| se erige / se posiciona / se consolida como | es / se volvió |
| viene a ser | es |
| funge / opera como | actúa como / es |

## 3. Frases-marco y arranques `[IA-LEXICO]`

- "En un mundo cada vez más [globalizado / digital / complejo]…"
- "No es casualidad que…" / "No es de extrañar que…"
- "Desde tiempos inmemoriales…" / "A lo largo de la historia…"
- "Ahora bien, …" (como bisagra automática)
- "Dicho esto, …"
- "En este sentido, …" (relleno de transición)
- "Por otro lado, …" sin un "por un lado" previo

## 4. Señalización y meta-comentario `[IA-SEÑAL]`

- "En este artículo / apartado / trabajo, exploraremos / veremos / analizaremos…"
- "A lo largo de este texto…"
- "Profundicemos / Exploremos / Analicemos…"
- "Sin más preámbulo…" / "Vamos a desglosar…"
- "Antes de continuar, conviene aclarar…"

## 5. Cierres y trascendencia `[IA-CIERRE]` / `[IA-SIGNIF]`

- "El futuro es prometedor" / "un futuro brillante" / "tiempos emocionantes"
- "Solo el tiempo lo dirá"
- "un paso en la dirección correcta"
- "queda mucho por recorrer / por explorar"
- "juega un papel crucial / fundamental / clave / pivotal"
- "marca un antes y un después" / "punto de inflexión" / "hito"
- "sienta las bases de" / "abre la puerta a"
- "deja una huella imborrable / indeleble"
- "testimonio / muestra / reflejo de"

## 6. Calcos del inglés (estilo) — falsos amigos de prosa

| Calco IA | Español |
|---|---|
| paisaje / panorama (de *landscape* abstracto) | situación / contexto / campo |
| robusto (*robust*) | sólido / fiable |
| en términos de (*in terms of*) | en cuanto a / respecto a |
| jugar un rol (*play a role*) | desempeñar un papel / influir |
| al final del día (*at the end of the day*) | en definitiva → mejor: eliminarlo |
| tener un impacto en (*have an impact*) | afectar a / influir en |
| altamente (*highly*) | muy / sumamente (con mesura) |
| crucial / pivotal (*pivotal*) | clave / decisivo (con mesura) |
| navegar (un reto) (*navigate*) | afrontar / gestionar |
| desbloquear (potencial) (*unlock*) | aprovechar / liberar |

> Nota: varios de estos también figuran en `corrector-prosa-castellana`
> (`[ANGLICISMO]`). Si el texto pide corrección normativa amplia, encadenar con
> ese skill.

## 7. Artefactos de chatbot `[IA-CHATBOT]` / `[IA-ADULA]`

- Aperturas: "¡Claro!", "¡Por supuesto!", "¡Excelente / Gran pregunta!", "Entendido."
- Entregas: "Aquí tienes…", "A continuación, te presento / dejo…"
- Cierres serviles: "Espero que esto (te) ayude / sirva", "¡Cualquier cosa, avísame!",
  "Si necesitas algo más, no dudes en pedírmelo."
- Ofertas: "¿Te gustaría que…?", "¿Quieres que lo amplíe / ajuste / desarrolle?"
- Disclaimers: "Como modelo de lenguaje…", "Hasta mi última actualización…",
  "Si bien la información es limitada…"
- Adulación: "Tienes toda la razón", "Excelente punto", "Como bien dices".

Todos se **eliminan**: son correspondencia de chat, no contenido.

---

## 8. Bancos de frases nuevos (v0.3)

### 8a. Conectores aditivos apilados `[IA-CONECTOR]`
Tell = pila de **aditivos**: además, asimismo, igualmente, del mismo modo, por otro
lado, en este sentido, de igual forma, sumado a ello. Los **concesivos** (sin
embargo, no obstante, aunque, si bien) son más humanos: no marcarlos aislados.

### 8b. Contraste retroactivo `[IA-CONTRASTE]`
"Contrario a lo que muchos piensan…", "Aunque suele creerse que…, en realidad…",
"Lejos de ser X, es Y", "A diferencia de la creencia popular…".

### 8c. Fórmulas de aforismo `[IA-AFORISMO]`
"X es el lenguaje de Y", "X es la moneda de Y", "la arquitectura de X", "X no es
una herramienta, es un espejo", "en el corazón de X late Y".

### 8d. Aperturas conversacionales `[IA-APERTURA]`
"¿La verdad?", "Mira,", "A ver,", "Seamos honestos,", "Te seré franco,", "Aquí va
la cosa:", "Vamos a ser claros:".

### 8e. Análisis con gerundio analítico `[IA-ANALISIS-ING]`
…reflejando, …subrayando, …consolidando, …lo que refleja·demuestra·evidencia un
compromiso con, …contribuyendo así a, …allanando el camino para.

### 8f. Sección formularia `[IA-SECCION]`
"Desafíos y perspectivas futuras", "A pesar de estos desafíos…", "De cara al
futuro", "Retos y oportunidades", "Conclusión y trabajo futuro" (cuando es relleno).

### 8g. Variedad del español (es-419) `[IA-VARIEDAD]`
Deslices peninsulares a vigilar en texto americano: ordenador→computadora,
coche→auto/carro, móvil→celular, zumo→jugo, patata→papa, vosotros→ustedes,
leísmo ("le vio"→"lo vio"), "vale"→"de acuerdo/listo", "gilipollez", "currar".
