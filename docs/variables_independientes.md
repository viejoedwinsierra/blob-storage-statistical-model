# Variables Independientes del Sistema

## 1. Definición

Las variables independientes corresponden a los factores que explican el comportamiento del sistema de almacenamiento desde la perspectiva del dataset construido.

Estas variables representan características observables del archivo, su ciclo de vida y condiciones operativas que influyen directamente en:

- el costo de almacenamiento
- el uso del sistema
- la calidad de los datos
- la dinámica del ciclo de vida

A diferencia de un enfoque puramente teórico, estas variables están definidas a nivel de **instancia (archivo)** y son medibles dentro del dataset.

---

## 2. Principio de Modelado

El objetivo del modelo es capturar relaciones funcionales entre variables observables que afectan:

- el costo del almacenamiento
- la persistencia del dato
- la eficiencia del sistema
- la aparición de anomalías

Por lo tanto:

> Las variables independientes no buscan replicar toda la complejidad del sistema distribuido, sino representar de forma controlada los factores que influyen en el comportamiento del almacenamiento.

---

## 3. Variables Independientes Definidas

### 3.1 Características del archivo

- `size_gb`: tamaño del archivo (variable clave del costo)
- `file_type`: tipo de archivo (json, jpg, pdf, mp4)

Estas variables determinan el volumen y la variabilidad del sistema.

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

Esta variable determina directamente:

- la tarifa de almacenamiento
- la eficiencia del uso de recursos

---

### 3.4 Variables operativas

- `transfer_duration_sec`: duración de transferencia
- `transfer_speed_mbps`: velocidad de transferencia

Estas variables permiten analizar:

- eficiencia del sistema
- comportamiento de ingestión de datos

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

Estas variables:

- presentan alta cardinalidad ⚠️
- no son interpretables directamente
- requieren transformación para uso en modelos

Su principal uso es en:

- machine learning
- detección de duplicados antes del almacenamiento

---

## 4. Uso en la Simulación

Las variables independientes son utilizadas para:

- generar el dataset sintético a nivel de archivo
- modelar el costo de almacenamiento
- simular comportamiento del ciclo de vida
- introducir condiciones de error
- analizar patrones de uso del sistema

---

## 5. Consideración Crítica

El modelo no busca reproducir exactamente la distribución real del sistema, sino construir un entorno controlado que permita:

- evaluar relaciones entre variables
- identificar patrones estructurales
- analizar impacto en costo

Esto implica:

- uso de distribuciones controladas
- simplificación de relaciones complejas
- separación entre modelo estadístico y modelo de machine learning

---

## 6. Principio Fundamental

> La validez del modelo depende de capturar correctamente las relaciones entre tamaño, tiempo, almacenamiento y costo, no de replicar exactamente los datos reales.

---

## 7. Relación con las 5Vs

Las variables independientes definen:

- Volume → size_gb
- Velocity → transferencia y acceso
- Variety → file_type
- Veracity → variables de error
- Value → optimización del storage_tier y ciclo de vida

---

## 8. Conclusión

Las variables independientes permiten construir un modelo coherente y controlado que:

- conecta características del archivo con costo
- integra ciclo de vida del dato
- incorpora calidad del sistema mediante errores
- habilita análisis estadístico y machine learning

Esto permite estudiar el comportamiento del almacenamiento desde una perspectiva estructural y cuantificable.
