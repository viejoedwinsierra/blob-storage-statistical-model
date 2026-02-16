---
🏠 [Inicio](../README.md)

⬅️ [Anterior](04_parametros_simulacion.md)

➡️ [Siguiente](06_preguntas_analiticas.md)

---

# 5. Modelamiento estadístico

## 5.1 Enfoque general

El modelamiento estadístico se construye a partir de un proceso generativo jerárquico donde:

1. Se determina el estado operativo del sistema (normal o incidente).
2. Se genera el volumen diario de blobs.
3. Se modela la proporción de duplicación por contenido.
4. Se derivan métricas agregadas (TPS, volumen acumulado, tasa de duplicación).

Este enfoque permite separar claramente:

- Variabilidad estructural del sistema.
- Efecto de incidentes.
- Comportamiento base del almacenamiento.
- Impacto acumulativo en costos.

---

## 5.2 Modelo de estado del sistema

El estado diario del sistema se modela como:

\[
I_t \sim \text{Bernoulli}(p_{incident})
\]

Donde:

- \(I_t = 0\): día normal  
- \(I_t = 1\): día con incidente  

Este modelo captura la naturaleza aleatoria de fallas operativas.

---

## 5.3 Modelo de conteo diario de blobs

El número total de blobs generados por día se modela como:

\[
X_t \sim
\begin{cases}
\text{Poisson}(\lambda) & I_t = 0 \\
\text{Poisson}(k\lambda) & I_t = 1
\end{cases}
\]

### Interpretación

- En operación normal, el sistema presenta una tasa media estable.
- En incidente, la tasa se amplifica por el factor \(k\), representando reintentos y reprocesos.

### Justificación del uso de Poisson

- Modela eventos independientes.
- Adecuado para conteos por unidad de tiempo.
- Escalable a volúmenes masivos (Big Data).
- Permite análisis de sobredispersión si se requiere extensión a Binomial Negativa.

---

## 5.4 Modelo de duplicación por contenido

Condicional al número de blobs generados en el día:

\[
D_t \sim \text{Binomial}(X_t, p_t)
\]

Donde:

\[
p_t =
\begin{cases}
p_{ok} & I_t = 0 \\
p_{fail} & I_t = 1
\end{cases}
\]

### Interpretación

- Cada blob tiene probabilidad \(p_t\) de replicar contenido existente.
- En incidente, la probabilidad de duplicación aumenta significativamente.

### Propiedad relevante

Dado que:

- \(X_t \sim Poisson(\lambda_t)\)
- \(D_t | X_t \sim Binomial(X_t, p_t)\)

Entonces:

\[
D_t \sim Poisson(p_t \lambda_t)
\]

Esto permite modelar duplicados como proceso de conteo independiente.

---

## 5.5 Modelo de tasa de duplicación observada

\[
duplicate\_rate_t = \frac{D_t}{X_t}
\]

Permite analizar:

- Cambios estructurales en la proporción.
- Detección de comportamiento anómalo.
- Comparación entre regímenes operativos.

---

## 5.6 Modelo de TPS (Transacciones por segundo)

Se define:

\[
TPS_t = \frac{X_t}{86400}
\]

Durante incidentes:

\[
\mathbb{E}[TPS_t | I_t=1] > \mathbb{E}[TPS_t | I_t=0]
\]

Interpretación:

- El aumento en TPS puede reflejar reintentos automáticos.
- TPS actúa como indicador temprano de degradación.

---

## 5.7 Hipótesis contrastables

### H1 – Incremento de volumen en incidentes

\[
H_0: \mathbb{E}[X_t | I_t=1] = \mathbb{E}[X_t | I_t=0]
\]

\[
H_1: \mathbb{E}[X_t | I_t=1] > \mathbb{E}[X_t | I_t=0]
\]

---

### H2 – Incremento en probabilidad de duplicación

\[
H_0: p_{fail} = p_{ok}
\]

\[
H_1: p_{fail} > p_{ok}
\]

---

### H3 – Incremento en TPS durante incidentes

\[
H_0: \mathbb{E}[TPS_t | I_t=1] = \mathbb{E}[TPS_t | I_t=0]
\]

\[
H_1: \mathbb{E}[TPS_t | I_t=1] > \mathbb{E}[TPS_t | I_t=0]
\]

---

## 5.8 Extensiones posibles

- Modelo de regresión Poisson:
  \[
  \log(\lambda_t) = \beta_0 + \beta_1 I_t
  \]

- Regresión logística para duplicación:
  \[
  \log\left(\frac{p_t}{1-p_t}\right) = \alpha_0 + \alpha_1 I_t
  \]

- Modelos de control estadístico para detección de spikes.

---

## 5.9 Relación con Big Data

El modelo es consistente con entornos de gran escala porque:

- Permite alta cardinalidad.
- Se basa en agregación diaria (reducción dimensional).
- Puede ejecutarse en frameworks distribuidos (Spark, MapReduce).
- Es extensible a terabytes de información.
- No requiere inspección de contenido completo (usa metadatos y hash).

---

## 5.10 Conclusión del modelamiento

El sistema puede interpretarse como un proceso de conteo condicionado por estado operativo, donde los incidentes alteran simultáneamente:

- La tasa de generación de blobs.
- La probabilidad de duplicación.
- La presión de escritura.
- El volumen acumulado.

Este enfoque permite cuantificar impacto operativo, riesgo financiero y salud del almacenamiento.
