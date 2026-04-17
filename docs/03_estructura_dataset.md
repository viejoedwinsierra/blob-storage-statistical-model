--
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---

# 3. Estructura y generación del dataset

## 3.1 Objetivo

Construir un dataset estructurado que represente el inventario de blobs generados por el sistema durante un horizonte de 180 días, incorporando tanto operación normal como eventos de falla que generan duplicación y anomalías en el almacenamiento.

El dataset modela instancias documentales observables y sirve como base para:

* Modelado estadístico del crecimiento
* Estimación del impacto en almacenamiento
* Identificación de anomalías
* Entrenamiento de modelos de machine learning
* Simulación de escenarios operativos

---

## 3.2 Definición formal de la unidad de análisis

Se define el dataset como:

$$
\mathcal{D} = {u_i}_{i=1}^{M_d}
$$

donde cada instancia $u_i$ corresponde a un **blob observable en el sistema de almacenamiento**.

Cada registro representa un archivo físico y contiene:

* identidad documental
* contenido parcial
* ubicación
* atributos temporales
* estado operativo (error/no error)

---

## 3.3 Fuente de datos

El dataset puede construirse bajo dos esquemas:

### 3.3.1 Dataset real

Derivado de Blob Storage mediante:

* enumeración completa de blobs
* extracción de metadatos físicos

### 3.3.2 Dataset simulado

Generado mediante un modelo probabilístico controlado que replica:

* llegada de eventos
* generación de archivos
* inyección de fallas
* duplicación de contenido

---

## 3.4 Estructura del dataset (tabla principal)

### Tabla: `blob_inventory`

| Campo                | Tipo      | Descripción                                                                          |
| -------------------- | --------- | ------------------------------------------------------------------------------------ |
| blob_id              | UUID      | Identificador único del registro                                                     |
| document_id          | string    | ID lógico del contenido                                                              |
| hash_head            | string    | Primeros N caracteres derivados del hash (tamaño configurable)                       |
| hash_tail            | string    | Últimos N caracteres derivados del hash (tamaño configurable)                        |
| blob_name            | string    | Nombre físico del archivo                                                            |
| container            | string    | Contenedor lógico                                                                    |
| path                 | string    | Ruta completa del blob                                                               |
| size_bytes           | integer   | Tamaño del archivo                                                                   |
| simulated_created_at | timestamp | Fecha simulada de creación                                                           |
| real_created_at      | timestamp | Fecha real del sistema                                                               |
| hour                 | integer   | Hora de generación (0–23)                                                            |
| is_error             | boolean   | Indicador de error                                                                   |
| error_type           | array     | Lista de tipos de error asociados al archivo (puede contener múltiples valores)      |
| error_group          | array     | Lista de categorías de error asociadas al archivo (puede contener múltiples valores) |

---

## 3.5 Enriquecimiento del contenido

Para capturar información parcial del contenido sin almacenar el documento completo, se definen:

$$
content_head = \text{primeros 200 caracteres}
$$

$$
content_tail = \text{últimos 200 caracteres}
$$

Esto permite:

* análisis de similitud
* detección de duplicados parciales
* trazabilidad del contenido
* reducción de volumen de datos

---

## 3.6 Detección de duplicación

Dos blobs se consideran duplicados si:

$$
hash_i = hash_j \quad \text{y} \quad name_i \neq name_j
$$

El número de duplicados diarios se define como:

$$
D_t = \sum_{h} (f(h) - 1), \quad \text{para } f(h) > 1
$$

donde $f(h)$ es la frecuencia de aparición del hash.

La tasa de duplicación es:

$$
duplicate_rate_t = \frac{D_t}{X_t}
$$

---

## 3.7 Tabla agregada diaria

### Tabla: `events_daily`

| Campo          | Descripción                         |
| -------------- | ----------------------------------- |
| day            | Fecha                               |
| $X_t$          | Número total de blobs               |
| $D_t$          | Número de duplicados                |
| duplicate_rate | $D_t / X_t$                         |
| incident_flag  | Indicador de incidente              |
| TPS_t          | Transacciones por segundo estimadas |

---

## 3.8 Modelo de generación (simulación)

### Llegada de eventos

$$
X_t \sim \text{Poisson}(\lambda)
$$

### Cambio de régimen

$$
X_t =
\begin{cases}
\text{Poisson}(\lambda) & \text{día normal} \
\text{Poisson}(k \cdot \lambda) & \text{incidente}
\end{cases}
$$

### Duplicación

$$
D_t \sim \text{Binomial}(X_t, p_{fail})
$$

---

## 3.9 Tipos de error modelados

| error_group | error_type             | Descripción                      |
| ----------- | ---------------------- | -------------------------------- |
| DUPLICATE   | SAME_CONTENT_DIFF_NAME | Mismo hash, distinto nombre      |
| DUPLICATE   | SAME_CONTENT_DIFF_PATH | Mismo contenido en ruta distinta |
| INTEGRITY   | NAME_COLLISION         | Mismo nombre, distinto contenido |
| MISSING     | PDF_WITHOUT_JSON       | Documento incompleto             |
| MISSING     | JSON_WITHOUT_PDF       | Metadata sin archivo             |

---

## 3.10 Validaciones estructurales

El dataset debe cumplir:

$$
\sum_{t} X_t = M_d
$$

$$
duplicate_rate_t \approx 0 \quad \text{en días normales}
$$

$$
duplicate_rate_t \approx \frac{k-1}{k} \quad \text{en incidentes}
$$

Además:

* consistencia entre hash y contenido
* coherencia entre timestamp y distribución temporal
* existencia física del archivo

---

## 3.11 Relación con el modelo estadístico

El volumen esperado se modela como:

$$
E[V] = \lambda \cdot S \cdot [(1 - p_{fail}) + p_{fail} \cdot k]
$$

donde:

* $S$: tamaño promedio del archivo
* $\lambda$: tasa de eventos
* $k$: factor de amplificación

---

## 3.12 Uso en machine learning

El dataset permite modelar:

$$
P(Y = 1 \mid X)
$$

donde:

* $Y$: indicador de error
* $X$: variables observables

Incluyendo:

* hora
* tamaño
* ruta
* duplicidad
* patrones de contenido

---

## 3.13 Consideraciones finales

Este dataset constituye una representación estructurada del sistema de almacenamiento, permitiendo:

* análisis reproducible
* validación de hipótesis
* entrenamiento de modelos
* comparación entre simulación y entorno real

La calidad del modelo predictivo dependerá directamente de la coherencia y consistencia de este dataset.
