---

🏠 [Inicio](../README.md)

⬅️ [Anterior](01_contexto_problema.md)

➡️ [Siguiente](03_estructura_dataset.md)

---

# 2. Procedencia y fuente de la base de datos

Se emplea un enfoque de **simulación controlada** debido a restricciones de acceso a datos reales completos en entornos productivos. Este enfoque permite construir un entorno experimental reproducible donde las condiciones del sistema pueden ser parametrizadas y evaluadas de forma sistemática.

La base de datos se genera mediante un **modelo generativo estructurado**, diseñado para replicar el comportamiento de un sistema distribuido de almacenamiento de objetos bajo diferentes condiciones operativas, incluyendo estados normales y escenarios de falla.

---

## 2.1 Generación del universo de contenidos únicos

Se define un conjunto inicial de contenidos únicos:

$$
N = 10{,}000
$$

Cada contenido representa una entidad documental base (*ground truth*) y se caracteriza por los siguientes atributos:

* `content_id`: identificador único del contenido
* `content_hash`: hash determinístico que representa el contenido lógico
* `size_bytes`: tamaño del archivo
* `creation_timestamp`: instante lógico de creación

El tamaño de los archivos se modela mediante una distribución probabilística:

$$
size_bytes \sim \text{LogNormal}(\mu, \sigma)
$$

Esta elección permite capturar la asimetría observada en tamaños de documentos reales, donde predominan archivos pequeños pero existen colas largas de mayor tamaño.

Este conjunto constituye la base del sistema, sobre la cual se generan instancias documentales, duplicados y variaciones operativas.

---

## 2.2 Generación de estructura jerárquica de almacenamiento

Para simular entornos empresariales reales, los objetos se distribuyen en una estructura jerárquica de almacenamiento.

Se define:

* $M$ contenedores, donde $M \in [5, 10]$

Cada objeto es asignado a una ruta de la forma:

$$
/empresa/{area}/{anio}/{mes}/{dia}/
$$

Esta estructura permite modelar:

* segmentación organizacional
* distribución temporal del almacenamiento
* concentración de carga por contenedor
* comportamiento no uniforme del sistema (efecto Pareto)

Formalmente, la asignación de rutas puede modelarse como una variable categórica:

$$
path_i \sim \mathcal{P}(\text{area}, \text{time})
$$

donde la probabilidad de asignación puede ser uniforme o sesgada para inducir concentración.

---

## 2.3 Generación de volumen diario de eventos

En condiciones normales, el número de eventos diarios se modela como un proceso de Poisson:

$$
X_t \sim \text{Poisson}(\lambda)
$$

donde:

* $X_t$: número de blobs generados en el día $t$
* $\lambda = 1000$: tasa promedio diaria

Este modelo captura la llegada de eventos independientes en el tiempo, consistente con sistemas transaccionales distribuidos.

---

## 2.4 Generación de duplicados

Se modelan distintos mecanismos de duplicación que representan fallas reales en sistemas distribuidos.

### Tipo A – Duplicado por contenido

* $content_hash$ se mantiene constante
* $blob_name$ cambia

$$
hash_i = hash_j \quad \text{y} \quad name_i \neq name_j
$$

Representa reintentos automáticos sin idempotencia.

---

### Tipo B – Colisión de nombre

* $blob_name$ es igual
* $content_hash$ es distinto

$$
name_i = name_j \quad \text{y} \quad hash_i \neq hash_j
$$

Representa errores de versionado o sobrescritura.

---

### Tipo C – Duplicado en rutas distintas

* $blob_name$ igual
* $content_hash$ igual
* $path$ distinto

$$
hash_i = hash_j,\quad name_i = name_j,\quad path_i \neq path_j
$$

Representa replicación entre áreas o inconsistencias de sincronización.

---

## 2.5 Generación de eventos de falla y cambio de régimen

Se introduce un mecanismo de cambio de régimen para modelar condiciones de incidente.

Se define una variable indicadora:

$$
incident_t \sim \text{Bernoulli}(p_{incident})
$$

donde:

$$
p_{incident} = 0.05
$$

En días normales:

$$
X_t \sim \text{Poisson}(\lambda)
$$

En días de incidente:

$$
X_t \sim \text{Poisson}(k \cdot \lambda)
$$

con:

$$
k = 3
$$

Adicionalmente, se modela la probabilidad de falla:

$$
D_t \sim \text{Binomial}(X_t, p_{fail})
$$

donde:

$$
p_{fail} = 0.10
$$

Este mecanismo permite capturar simultáneamente:

* incremento en volumen
* incremento en duplicidad
* correlación inducida por fallas

---

## 2.6 Construcción del dataset maestro

A partir de los procesos anteriores, se construye un **dataset maestro estructurado**, donde cada fila representa una instancia documental.

Formalmente:

$$
\mathcal{D} = {u_i}_{i=1}^{M_d}
$$

donde cada instancia $u_i$ contiene:

* atributos temporales
* atributos de contenido
* atributos de ruta
* indicadores de error
* metadatos de control

Este dataset constituye la fuente de verdad del sistema simulado y es utilizado posteriormente para:

* generación de archivos físicos
* construcción de inventarios
* entrenamiento de modelos estadísticos y de machine learning

---

## 2.7 Alcance analítico de la simulación

El entorno generado permite evaluar múltiples escenarios:

* crecimiento acumulativo del almacenamiento
* impacto de la duplicidad en consumo de recursos
* sensibilidad al factor de amplificación $k$
* sensibilidad a la probabilidad de falla $p_{fail}$
* concentración de carga por contenedor
* comportamiento bajo alta presión de micro-objetos

Estos escenarios permiten analizar el sistema en diferentes escalas:

* nivel GB
* nivel TB
* escenarios de alta concurrencia

---

## 2.8 Limitaciones del enfoque

Aunque el modelo busca capturar propiedades estructurales del sistema real, presenta las siguientes limitaciones:

* independencia parcial de eventos en condiciones simuladas
* simplificación de dependencias entre servicios
* ausencia de factores externos no modelados (red, latencia, throttling)

Por lo tanto, los resultados deben interpretarse como aproximaciones controladas, útiles para análisis comparativo y diseño de hipótesis, pero no como representación exacta del sistema real.
