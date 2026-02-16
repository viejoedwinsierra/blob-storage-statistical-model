---
🏠 [Inicio](../README.md)

⬅️ [Anterior](01_contexto_problema.md)

➡️ [Siguiente](03_estructura_dataset.md)

---

# 2. Procedencia y fuente de la base de datos

Se emplea simulación controlada debido a restricciones de acceso a datos reales.

La base se construye mediante un modelo generativo estructurado que replica el comportamiento de un sistema distribuido de almacenamiento.

---

## 2.1 Generación del universo de contenidos únicos

Se define un conjunto inicial:

- N = 10,000 contenidos únicos.
- Cada contenido posee:
  - content_id (identificador verdadero).
  - content_hash (hash determinístico).
  - size_bytes (distribución centrada en 1MB).
  - timestamp de creación.

El tamaño puede generarse con una distribución normal truncada o log-normal para simular variabilidad realista.

Este conjunto define el ground truth del sistema.

---

## 2.2 Generación de estructura jerárquica de directorios

Para simular entornos empresariales reales, los blobs se distribuyen en:

- M contenedores (ej. 5–10).
- Estructura jerárquica de paths tipo:

  /empresa/{area}/{anio}/{mes}/{dia}/

Esto permite:

- Análisis por segmento organizacional.
- Medición de concentración por área.
- Evaluación de presión por contenedor.
- Análisis de distribución desigual (efecto Pareto).

---

## 2.3 Generación de volumen diario

En condiciones normales:

X_t ~ Poisson(λ)

con λ = 1000 blobs/día.

Esto modela la llegada de eventos transaccionales independientes.

---

## 2.4 Generación de duplicados

Se modelan tres tipos de duplicación:

### Tipo A – Duplicado por contenido (hash igual, nombre distinto)

- Se replica content_hash.
- Se modifica blob_name (suffix _retry, _copy, timestamp).
- Representa reintentos automáticos.

### Tipo B – Colisión de nombre (nombre igual, hash distinto)

- Dos contenidos distintos comparten blob_name.
- Representa errores de versionado.

### Tipo C – Duplicado exacto en diferente path

- Hash y nombre iguales.
- path distinto.
- Representa replicación entre áreas.

---

## 2.5 Generación de eventos de falla

Se define:

- 5% de los días como incident_flag = 1.
- En dichos días:

X_t ~ Poisson(kλ) con k = 3.

Además:

D_t ~ Binomial(X_t, p_fail)

con p_fail = 0.10.

Esto incrementa tanto volumen como probabilidad de duplicación.

---

## 2.6 Presión de almacenamiento y análisis variable

La simulación permite analizar diferentes escenarios:

- Alta presión por micro-blobs (≤1MB).
- Concentración en pocos contenedores.
- Alta dispersión de duplicados.
- Crecimiento acumulativo mensual.
- Sensibilidad al factor k.
- Sensibilidad a p_fail.

Esto permite estudiar:

- Escenarios GB.
- Escenarios TB.
- Escalabilidad computacional.
- Robustez de los modelos de conteo.
