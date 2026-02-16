---
🏠 [Inicio](../README.md)

⬅️ [Anterior](03_estructura_dataset.md)

➡️ [Siguiente](05_modelamiento_estadistico.md)

---

# 4. Parámetros de simulación

## 4.1 Propósito de la simulación

La simulación permite generar un dataset sintético que reproduzca el comportamiento del almacenamiento (Blob Storage) bajo dos regímenes operativos:

1) **Operación normal**: el volumen de blobs crece proporcionalmente al volumen transaccional.  
2) **Operación degradada (incidente)**: por timeouts y reintentos, el sistema genera **duplicados por contenido** (mismo `content_hash`), incrementando de forma abrupta el número de blobs y el volumen total almacenado.

El resultado de la simulación debe producir las tablas definidas en `03_estructura_dataset.md`:

- `blob_inventory` (nivel archivo/blob)
- `events_daily` (agregación diaria)

---

## 4.2 Horizonte y tamaño del experimento

### 4.2.1 Horizonte temporal
- **T = días = 180**
- Interpretación: se simula un período continuo (por ejemplo, 6 meses) para observar efectos acumulativos y estacionalidad simple.

### 4.2.2 Tamaño muestral
- **N = 10,000**
- Uso: tamaño de población base para:
  - simulación Monte Carlo (N corridas), o
  - universo de transacciones/identificadores si se quiere modelar colisiones/reintentos sobre un set finito.

> Recomendación práctica:
> - Si estás haciendo **una sola línea temporal de 180 días**, N puede interpretarse como el tamaño del “pool” de claves/hashes potenciales.
> - Si estás haciendo **simulación Monte Carlo**, N representa el número de ejecuciones de la simulación para estimar intervalos y distribuciones (medias, percentiles).

---

## 4.3 Parámetros operacionales del sistema

### 4.3.1 λ (lambda) — tasa media diaria de transacciones
- **λ = 1000 transacciones/día**
- Uso: controla el volumen esperado de blobs en operación normal.
- Modelo:
  - En día normal:  
    \[
    X_t \sim \text{Poisson}(\lambda)
    \]
- Interpretación: el sistema tiene una tasa media estable con variabilidad natural (Poisson).

**Lectura de negocio**: si λ sube, el storage crece linealmente incluso sin incidentes.

---

### 4.3.2 k — factor de amplificación durante incidentes
- **k = 3**
- Uso: representa multiplicación del volumen por reintentos (timeouts → reenvíos → duplicados).
- Modelo:
  - En día con falla:  
    \[
    X_t \sim \text{Poisson}(k\lambda)
    \]
- Interpretación: durante un incidente, la plataforma efectivamente procesa (o intenta procesar) ~k veces la carga normal.

**Lectura técnica**: k agrega “burst” de escritura y explica picos de volumen, aun sin aumento real de usuarios.

---

## 4.4 Parámetros de confiabilidad (estado del día)

### 4.4.1 Probabilidad de incidente por día
- En el documento aparecen:
  - **p_fail = 0.10**
  - y también “**5% días con falla**”

⚠️ Debe quedar un **solo valor** para consistencia.  
Recomendación: define:

- **p_incident = 0.05** (si el supuesto es 5% días con falla)

y deja **p_fail = p_incident**.

#### Variable de estado del día
\[
I_t \sim \text{Bernoulli}(p_{\text{incident}})
\]
- Si \(I_t = 0\): día normal  
- Si \(I_t = 1\): día con incidente

---

## 4.5 Parámetros de duplicación por contenido

### 4.5.1 p_ok y p_fail — tasa de duplicación según el estado

- **p_ok = 0.02**  
  - En días normales, se permite una pequeña tasa de duplicación (ej: reintentos puntuales, reprocesos marginales, “at least once delivery”).
- **p_fail = 0.10**  
  - En días con incidente, la duplicación aumenta por reintentos sistemáticos.

#### Definición del parámetro efectivo diario
\[
p_t =
\begin{cases}
p_{ok} & \text{si } I_t = 0 \\
p_{fail} & \text{si } I_t = 1
\end{cases}
\]

### 4.5.2 Modelo de duplicados por día
Dado el total de blobs creados en el día \(X_t\), los duplicados (por contenido) se modelan como:

\[
D_t \sim \text{Binomial}(X_t, p_t)
\]

Donde:
- \(D_t\) = cantidad de blobs cuyo contenido **replica** contenido ya existente (hash repetido)
- \(X_t\) = total de blobs creados ese día
- \(p_t\) = probabilidad de que un blob sea duplicado (dependiendo del estado)

---

## 4.6 Relación con TPS (Transacciones por segundo)

Para enriquecer `events_daily.TPS_t`, se puede estimar:

\[
TPS_t = \frac{X_t}{86400}
\]

> Ajuste opcional (mejor realismo):
> durante incidentes, TPS observado puede **aumentar** por reintentos aunque la demanda real no crezca.  
> Esto se modela naturalmente porque \(X_t\) crece por k.

---

## 4.7 Tamaño de archivo y volumen diario

Sea:
- **S = 1MB por blob** (o en bytes: ~1,048,576)

Volumen diario:

\[
V_t = X_t \cdot S
\]

Volumen acumulado:

\[
V_{1:T} = \sum_{t=1}^{T} V_t
\]

---

## 4.8 Resumen de parámetros (tabla)

| Parámetro | Valor | Unidad | Significado | Impacto principal |
|----------|------:|--------|-------------|------------------|
| T (días) | 180 | días | Horizonte de simulación | Efecto acumulado |
| λ | 1000 | tx/día | Tasa media normal | Crecimiento base |
| k | 3 | factor | Amplificación en incidentes | Picos + duplicados |
| p_incident | 0.05 (o 0.10) | prob/día | Frecuencia de días con falla | Frecuencia de picos |
| p_ok | 0.02 | prob/blob | Duplicación normal | Ruido de base |
| p_fail | 0.10 | prob/blob | Duplicación en falla | Duplicados masivos |
| S | 1MB | bytes/blob | Tamaño promedio archivo | Costos storage |

---

## 4.9 Escenarios de simulación recomendados (para análisis comparativo)

Para que el análisis sea más robusto, se sugieren al menos estos escenarios:

1) **Baseline**: p_incident bajo (1–2%), k moderado (2–3)  
2) **Incidentes frecuentes**: p_incident alto (10%), k moderado  
3) **Incidente severo**: p_incident medio (5%), k alto (5–8)  
4) **Duplicación alta sin aumentar volumen**: k ~ 1 pero p_fail alto (simula reescrituras/reprocesos)

Esto permite comparar sensibilidad del crecimiento y costo.

---

## 4.10 Distribuciones principales (definición final)

### Estado del día
\[
I_t \sim \text{Bernoulli}(p_{\text{incident}})
\]

### Blobs generados
\[
X_t \sim
\begin{cases}
\text{Poisson}(\lambda) & I_t = 0 \\
\text{Poisson}(k\lambda) & I_t = 1
\end{cases}
\]

### Duplicados por contenido
\[
D_t \sim \text{Binomial}(X_t, p_t)
\]

### Tasa de duplicación observada
\[
duplicate\_rate_t = \frac{D_t}{X_t}
\]

---

## 4.11 Notas de consistencia (importante)

- Si se mantiene “**5% días con falla**”, entonces:
  - usar **p_incident = 0.05** (y retirar p_fail como probabilidad diaria)
- Reservar **p_fail** exclusivamente para “probabilidad de duplicación por blob” en día con incidente.

Esto evita ambigüedad y hace el modelo reproducible.

---
