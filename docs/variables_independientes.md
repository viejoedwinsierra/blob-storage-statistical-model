# Variables Independientes del Sistema

## 1. Definición

Las variables independientes corresponden a los factores observables a nivel de archivo que explican el comportamiento del sistema de almacenamiento.

Estas variables representan características del archivo, su ciclo de vida y condiciones operativas que influyen en:

- el volumen de almacenamiento
- el uso del sistema
- la calidad de los datos
- la dinámica del ciclo de vida

⚠️ Las variables independientes NO incluyen el costo, sino los factores que lo generan.

---

## 2. Principio de Modelado

El objetivo del modelo es capturar relaciones funcionales entre variables observables que permiten explicar:

- el costo del almacenamiento (variable dependiente)
- la persistencia del dato
- la eficiencia del sistema
- la aparición de anomalías

Por lo tanto:

> Las variables independientes representan las causas del comportamiento del sistema, mientras que el costo y otras métricas son efectos derivados.

---

## 3. Variables Independientes Definidas

### 3.1 Características del archivo

- `size_gb`: tamaño del archivo (principal determinante del volumen)
- `file_type`: tipo de archivo (json, jpg, pdf, mp4)

Estas variables determinan la estructura y variabilidad del almacenamiento.

---

### 3.2 Ciclo de vida del dato

- `days_stored`: tiempo total de almacenamiento
- `days_since_last_access`: tiempo desde último acceso
- `read_level`: nivel de lectura (low, medium, high)
- `modify_level`: nivel de modificación
- `movement_storage`: indicador de cambio de tier

Estas variables modelan el comportamiento dinámico del dato en el tiempo.

---

### 3.3 Infraestructura de almacenamiento

- `storage_tier`: nivel de almacenamiento (hot, cool, archive)

Esta variable determina:

- la tarifa de almacenamiento (de forma indirecta)
- la eficiencia del uso de recursos

---

### 3.4 Variables operativas

- `transfer_duration_sec`: duración de transferencia
- `transfer_speed_mbps`: velocidad de transferencia

Estas variables permiten analizar:

- eficiencia del sistema
- comportamiento de ingestión de datos

⚠️ `transfer_speed_mbps` es una variable derivada, lo que puede introducir dependencia con otras variables.

---

### 3.5 Variables de error (calidad del dato)

- `error_duplicado`
- `error_orphan`
- `error_null`
- `error_blob_timeout`

Estas variables representan condiciones anómalas del sistema y afectan:

- la veracidad del dato
- la consistencia del almacenamiento

Se modelan como variables binarias:

\[
error \in \{0,1\}
\]

---

### 3.6 Variables derivadas de contenido (hash)

- `hash_head`
- `hash_tail`

Estas variables representan segmentos parciales del contenido del archivo y se utilizan como:

- proxy probabilístico de persistencia
- indicador de recurrencia del contenido
- mecanismo aproximado de detección de duplicados

⚠️ No se utiliza el hash completo debido a su alto costo computacional.

⚠️ Estas variables presentan alta cardinalidad y no son interpretables directamente.

Su principal uso es en:

- machine learning
- detección de duplicados antes del almacenamiento

---

## 4. Uso en el Modelo

Las variables independientes son utilizadas para:

- explicar el costo de almacenamiento (Y)
- modelar el comportamiento del ciclo de vida
- analizar patrones de uso del sistema
- identificar condiciones de error

---

## 5. Consideración Crítica

El modelo no busca reproducir exactamente la distribución real del sistema, sino construir un entorno controlado que permita:

- evaluar relaciones causales entre variables
- identificar patrones estructurales
- analizar impacto en costo

Esto implica:

- uso de distribuciones controladas
- simplificación de relaciones complejas
- separación entre variables causales (X) y resultados (Y)

---

## 6. Principio Fundamental

> Las variables independientes representan los factores que generan el comportamiento del sistema, mientras que el costo y las métricas agregadas son consecuencias de dichas variables.

---

## 7. Relación con las 5Vs

Las variables independientes definen:

- Volume → size_gb
- Velocity → transferencia y acceso
- Variety → file_type
- Veracity → variables de error
- Value → emerge del modelo (no es una variable independiente)

---

## 8. Conclusión

Las variables independientes permiten construir un modelo coherente donde:

- el costo es una consecuencia del sistema
- el comportamiento emerge de las características del archivo
- las anomalías se integran como condiciones explicativas

Esto garantiza consistencia entre dataset, modelo estadístico y análisis.
