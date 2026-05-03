---
🏠 [Inicio](../README.md)

⬅️ [Anterior](03_00_estructura_dataset.md)

➡️ [Siguiente](05_modelamiento_estadistico.md)

---

# 4. Parámetros de simulación

## 4.1 Propósito de la simulación

La simulación genera un dataset sintético a nivel de archivo que replica el comportamiento de un sistema de almacenamiento bajo condiciones controladas.

El modelo integra:

- características del archivo
- distribución temporal de carga
- errores estructurados por tipo
- persistencia probabilística del contenido
- ciclo de vida del dato

El resultado es la tabla:

- `blob_inventory` (nivel archivo)

---

## 4.2 Configuración general del simulador

El volumen diario de archivos está acotado por:

$$
N_t \leq N_{max}
$$

donde:

$$
N_{max} = 10{,}000
$$

y:

$$
N_t = \text{número de archivos generados en el día } t
$$

El porcentaje global de error se define como:

$$
P(error) = 0.24
$$

---

## 4.3 Distribución temporal de la carga

La generación diaria se distribuye en franjas horarias:

$$
N_t = \sum_{h=1}^{H} N_{t,h}
$$

donde:

$$
N_{t,h} \sim \text{Categorical}(p_h)
$$

y:

$$
p_h = \{0.10, 0.20, 0.50, 0.20\}
$$

Esto representa las proporciones de carga por franja del día.

La tasa de error por franja se modela como:

$$
P(error \mid h) = 0.25
$$

---

## 4.4 Distribución jerárquica de errores

El error global se descompone como:

$$
P(error) = \sum_{f=1}^{F} P(f_f) \cdot P(s \mid f_f)
$$

donde:

- \(f_f\): familia de error  
- \(s\): subtipo  

Ejemplo:

$$
P(\text{duplicidad}) = 0.40
$$

---

## 4.5 Generación de errores a nivel archivo

Cada archivo tiene una probabilidad de error:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

donde:

$$
p_i = P(error) \cdot P(f_f) \cdot P(s \mid f_f)
$$

---

## 4.6 Distribución de tamaño

El tamaño del archivo se modela como:

$$
size_{gb} \sim \text{LogNormal}(\mu, \sigma)
$$

condicionado por el tipo:

$$
size_{gb} \mid file_{type}
$$

---

## 4.7 Distribución del tipo de archivo

El tipo de archivo se define como:

$$
file_{type} \sim \text{Categorical}(json, jpg, pdf, mp4)
$$

---

## 4.8 Persistencia del contenido

El contenido se representa mediante segmentos de hash:

$$
hash_{head} = H(content)_{[0:k]}
$$

$$
hash_{tail} = H(content)_{[-k:]}
$$

Estas variables actúan como aproximación probabilística de persistencia.

---

## 4.9 Modelo de variables

El conjunto de variables independientes es:

$$
X = \{size_{gb}, file_{type}, storage_{tier}, days_{stored}, error_i, hash_{head}, hash_{tail}\}
$$

Las variables dependientes se definen como:

$$
Y = \{storage_{cost}, has_{error}, is_{duplicate}\}
$$

El objetivo del modelo es estimar:

$$
P(Y \mid X)
$$

---

## 4.10 Función de costo

El costo se calcula como:

$$
storage_{cost} = size_{gb} \cdot rate_{tier} \cdot \frac{days_{stored}}{30}
$$

---

## 4.11 Resumen del modelo probabilístico

El sistema se puede resumir como:

$$
X \rightarrow Y
$$

y de forma probabilística:

$$
P(Y \mid X)
$$

---

## 4.12 Notas de consistencia

- Todas las ecuaciones se presentan en formato bloque.
- Se evita el uso de notación inline.
- Se separa claramente definición matemática de interpretación.
- El modelo es probabilístico, no determinista.
- El uso de hash es parcial para eficiencia ⚠️.

---

## 4.13 Conclusión

La simulación integra:

- generación de datos
- distribución temporal
- errores jerárquicos
- variables observables

Esto permite construir un dataset coherente para análisis estadístico y modelamiento probabilístico.
