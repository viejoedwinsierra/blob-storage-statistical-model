🏠 [Inicio](../README.md)

⬅️ [Anterior](03_00_estructura_dataset.md)
➡️ [Siguiente](05_modelamiento_estadistico.md)

---

# 4. Parámetros de simulación

## 4.1 Propósito de la simulación

La simulación tiene como propósito generar datasets sintéticos a nivel de archivo que representen el comportamiento de un sistema de almacenamiento tipo Blob Storage bajo condiciones controladas.

El simulador permite crear múltiples escenarios variando parámetros como:

* volumen de archivos generados
* distribución diaria de carga
* tipos de archivo
* tamaño de archivos
* niveles de almacenamiento
* probabilidad de errores
* comportamiento de acceso y ciclo de vida

El dataset resultante se utiliza como base para:

* análisis descriptivo
* análisis multivariado
* premodelamiento
* modelamiento estadístico
* evaluación con nuevos datasets

El repositorio utilizado para la generación de datos es:

🔗 https://github.com/viejoedwinsierra/mvp_thesis_simulator

---

## 4.2 Construcción de los datasets

Los datasets se construyen mediante ejecuciones controladas del simulador. Cada ejecución genera una tabla a nivel de archivo, donde cada fila representa un objeto almacenado.

La tabla principal generada es:

* `blob_inventory`

Cada registro contiene información asociada a:

* características del archivo
* tipo de contenido
* tamaño
* nivel de almacenamiento
* tiempo de permanencia
* variables operativas
* errores simulados
* costo calculado

Formalmente, cada dataset puede representarse como:

$$
\mathcal{D}^{(k)} = {u_i^{(k)}}_{i=1}^{N}
$$

donde:

* ( \mathcal{D}^{(k)} ): dataset generado en la ejecución (k)
* (u_i): archivo simulado
* (N): número total de archivos generados

Esto permite generar un dataset para modelamiento y otro dataset nuevo para evaluación, sin reentrenar los modelos.

---

## 4.3 Configuración general del simulador

El volumen total de archivos generados se controla mediante un parámetro máximo:

$$
N \leq N_{max}
$$

donde:

$$
N_{max} = 10{,}000
$$

Este valor representa el tamaño base del experimento.

Sin embargo, este parámetro puede variar para representar distintos escenarios:

| Escenario     | Número de archivos | Interpretación                      |
| ------------- | -----------------: | ----------------------------------- |
| Bajo volumen  |      1.000 - 5.000 | Sistema pequeño o prueba controlada |
| Volumen medio |             10.000 | Escenario base del estudio          |
| Alto volumen  |   50.000 - 100.000 | Simulación de operación empresarial |
| Estrés        |          > 100.000 | Evaluación de escalabilidad         |

---

## 4.4 Configuración temporal mediante `time_config`

El simulador permite distribuir la generación de archivos a lo largo del día mediante una configuración temporal.

La carga diaria se divide en franjas horarias:

$$
N_t = \sum_{h=1}^{H} N_{t,h}
$$

donde:

* (N_t): número total de archivos generados en el día (t)
* (N_{t,h}): número de archivos generados en la franja horaria (h)
* (H): número de franjas horarias

La distribución de carga por franja se define como:

$$
p_h = {0.10, 0.20, 0.50, 0.20}
$$

Esto representa un comportamiento operativo donde la mayor parte de la carga ocurre en una franja central del día.

---

## 4.5 Interpretación de la carga diaria

La configuración anterior puede interpretarse así:

| Franja                 | Proporción de carga | Interpretación                       |
| ---------------------- | ------------------: | ------------------------------------ |
| Baja actividad inicial |                 10% | Inicio del día o baja demanda        |
| Actividad media        |                 20% | Incremento progresivo de operaciones |
| Pico operativo         |                 50% | Mayor concentración de carga         |
| Descenso operativo     |                 20% | Reducción gradual de actividad       |

Este comportamiento permite simular escenarios más realistas que una generación uniforme de archivos.

En términos prácticos, el parámetro `time_config` permite responder preguntas como:

* ¿Qué ocurre si la carga se concentra en pocas horas?
* ¿Cómo cambia la duración de transferencia bajo picos de operación?
* ¿Qué impacto tiene la congestión sobre errores o tiempos?
* ¿Cómo se comporta el costo cuando el sistema opera con alta variabilidad diaria?

---

## 4.6 Variación de escenarios mediante `time_config`

La configuración temporal puede modificarse para representar distintos contextos empresariales.

### Escenario uniforme

$$
p_h = {0.25, 0.25, 0.25, 0.25}
$$

Representa una operación estable durante todo el día.

---

### Escenario con pico central

$$
p_h = {0.10, 0.20, 0.50, 0.20}
$$

Representa el escenario base utilizado en el estudio.

---

### Escenario de alta congestión

$$
p_h = {0.05, 0.10, 0.70, 0.15}
$$

Representa una operación con fuerte concentración de carga en una franja específica.

---

### Escenario nocturno o batch

$$
p_h = {0.05, 0.10, 0.15, 0.70}
$$

Representa procesos batch ejecutados principalmente al final del día.

---

## 4.7 Distribución de errores

El porcentaje global de error se define como:

$$
P(error) = 0.24
$$

Esto significa que una proporción del universo simulado puede presentar algún tipo de error.

Los errores se modelan como eventos binarios:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

donde:

* (error_i = 1): el archivo presenta error
* (error_i = 0): el archivo no presenta error

---

## 4.8 Distribución jerárquica de errores

El error global puede descomponerse en familias o tipos de error.

Ejemplos:

* duplicidad
* archivo huérfano
* valores nulos
* timeout de Blob Storage

Formalmente:

$$
P(error) = \sum_{f=1}^{F} P(f) \cdot P(s \mid f)
$$

donde:

* (f): familia de error
* (s): subtipo de error

Por ejemplo:

$$
P(\text{duplicidad}) = 0.40
$$

Esto permite simular no solo la existencia de errores, sino también su composición interna.

---

## 4.9 Generación de errores a nivel archivo

Cada archivo puede recibir una marca de error según una distribución Bernoulli:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

El parámetro (p_i) puede depender de:

* probabilidad global de error
* tipo de error
* subtipo de error
* condiciones de carga
* franja horaria

De esta forma, el simulador puede representar escenarios donde los errores aumentan en momentos de mayor congestión.

---

## 4.10 Distribución del tamaño de archivo

El tamaño del archivo se modela mediante una distribución LogNormal:

$$
size_{gb} \sim \text{LogNormal}(\mu, \sigma)
$$

Esta elección es coherente con sistemas reales de almacenamiento, donde suele observarse:

* alta concentración de archivos pequeños
* menor frecuencia de archivos grandes
* presencia de cola larga

El tamaño también puede condicionarse por el tipo de archivo:

$$
size_{gb} \mid file_{type}
$$

Esto permite representar que distintos tipos de archivo tienen patrones de tamaño diferentes.

---

## 4.11 Distribución del tipo de archivo

El tipo de archivo se modela como una variable categórica:

$$
file_{type} \sim \text{Categorical}(json, jpg, pdf, mp4)
$$

Esta variable permite simular variedad de contenido.

Ejemplo de variación posible:

| Tipo de archivo | Escenario documental | Escenario multimedia |
| --------------- | -------------------: | -------------------: |
| json            |                 Alto |                Medio |
| jpg             |                Medio |                 Alto |
| pdf             |                 Alto |                Medio |
| mp4             |                 Bajo |                 Alto |

Así, el simulador puede representar distintos universos de datos según el tipo de empresa o caso de uso.

---

## 4.12 Nivel de almacenamiento

El nivel de almacenamiento se modela como variable categórica:

$$
storage_{tier} \sim \text{Categorical}(hot, cool, archive)
$$

Cada nivel representa una política diferente de acceso y costo:

| Tier    | Uso esperado          | Costo relativo                |
| ------- | --------------------- | ----------------------------- |
| hot     | acceso frecuente      | mayor costo de almacenamiento |
| cool    | acceso ocasional      | costo intermedio              |
| archive | acceso poco frecuente | menor costo de almacenamiento |

Esta variable es fundamental porque participa directamente en el cálculo del costo.

---

## 4.13 Ciclo de vida del dato

El ciclo de vida se representa mediante variables como:

* `days_stored`
* `days_since_last_access`
* `read_level`
* `modify_level`
* `movement_storage`

Estas variables permiten simular:

* archivos activos
* archivos fríos
* archivos archivados
* archivos candidatos a movimiento de tier

El tiempo de almacenamiento puede representarse como:

$$
days_{stored} \sim F_t
$$

donde (F_t) puede variar según el escenario:

| Distribución | Uso                                            |
| ------------ | ---------------------------------------------- |
| Uniforme     | permanencias balanceadas                       |
| Exponencial  | muchos archivos recientes y pocos muy antiguos |
| Sesgada      | acumulación histórica                          |

---

## 4.14 Persistencia del contenido

Para representar contenido sin almacenar archivos reales, el simulador utiliza segmentos de hash:

$$
hash_{head} = H(content)_{[0:k]}
$$

$$
hash_{tail} = H(content)_{[-k:]}
$$

Estos campos funcionan como aproximaciones para:

* persistencia del contenido
* recurrencia
* posible duplicidad

Este mecanismo reduce costo computacional, aunque no equivale a una deduplicación exacta basada en hash completo.

---

## 4.15 Cálculo del costo

El costo de almacenamiento se calcula mediante:

$$
storage_{cost} =
size_{gb} \cdot rate_{tier} \cdot \frac{days_{stored}}{30}
$$

donde:

* (size_{gb}): tamaño del archivo
* (rate_{tier}): tarifa asociada al nivel de almacenamiento
* (days_{stored}): tiempo de permanencia

Esto refleja una lógica similar a la facturación cloud, donde el costo depende del volumen, el tiempo y la clase de almacenamiento.

---

## 4.16 Modelo de variables

El conjunto de variables independientes se define como:

$$
X = {size_{gb}, file_{type}, storage_{tier}, days_{stored}, error_i, hash_{head}, hash_{tail}}
$$

Las variables dependientes son:

$$
Y = {storage_{cost}, has_{error}, is_{duplicate}, transfer_{duration}}
$$

El objetivo del modelamiento es estimar:

$$
P(Y \mid X)
$$

---

## 4.17 Variación de parámetros para representar más casos de uso

Una ventaja central del simulador es que los parámetros pueden modificarse para representar un universo más amplio de escenarios.

| Parámetro                   | Puede variar para representar |
| --------------------------- | ----------------------------- |
| `N`                         | tamaño del sistema            |
| `time_config`               | patrón de carga diaria        |
| `file_type_distribution`    | tipo de empresa o contenido   |
| `size_distribution`         | archivos livianos o pesados   |
| `storage_tier_distribution` | política de almacenamiento    |
| `error_probability`         | calidad del sistema           |
| `days_stored_distribution`  | ciclo de vida del dato        |
| `hash_persistence`          | recurrencia o duplicidad      |

---

## 4.18 Ejemplos de escenarios simulables

### Escenario 1: empresa documental

* alto porcentaje de PDF y JSON
* tamaños moderados
* bajo volumen multimedia
* permanencia media o alta

---

### Escenario 2: empresa multimedia

* alto porcentaje de imágenes y videos
* archivos grandes
* alto costo de almacenamiento
* mayor necesidad de lifecycle management

---

### Escenario 3: sistema con alta congestión

* carga concentrada en pocas horas
* mayor presión sobre transferencia
* posible aumento de errores operativos

---

### Escenario 4: almacenamiento histórico

* muchos archivos antiguos
* baja frecuencia de acceso
* oportunidad de mover datos a `archive`

---

## 4.19 Resumen del modelo probabilístico

El sistema puede resumirse como:

$$
X \rightarrow Y
$$

donde las variables de entrada generan respuestas observables.

De forma probabilística:

$$
P(Y \mid X)
$$

representa el comportamiento que los modelos estadísticos buscan capturar.

---

## 4.20 Notas de consistencia

* La simulación es controlada, no observacional.
* Los parámetros deben interpretarse como supuestos experimentales.
* Cada dataset depende de la configuración usada en el simulador.
* La evaluación con dataset nuevo permite analizar estabilidad.
* El uso de hash parcial mejora eficiencia, pero introduce incertidumbre.

---

## 4.21 Conclusión

La simulación permite construir datasets controlados, reproducibles y parametrizables para estudiar el comportamiento de sistemas de almacenamiento cloud.

El uso de parámetros configurables, especialmente `time_config`, permite representar diferentes patrones de carga diaria y ampliar el universo de casos de uso simulables.

Esto fortalece el proyecto porque permite pasar de un único dataset estático a un entorno experimental donde se pueden evaluar múltiples escenarios, hipótesis y modelos.

---
