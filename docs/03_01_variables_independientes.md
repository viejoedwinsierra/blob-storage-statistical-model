---
🏠 [Inicio](../README.md)

⬅️ [Anterior](03_estructura_dataset.md)

➡️ [Siguiente](03_02_variables_dependientes.md)

---

# Variables Independientes del Sistema

## 🔗 Navegación del modelo

⬅️ [Volver al Dataset](03_estructura_dataset.md)  
➡️ [Ir a Variables Dependientes (Y)](03_02_variables_dependientes.md)

---

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

---

### 3.2 Ciclo de vida del dato

- `days_stored`: tiempo total de almacenamiento
- `days_since_last_access`: tiempo desde último acceso
- `read_level`: nivel de lectura (low, medium, high)
- `modify_level`: nivel de modificación
- `movement_storage`: indicador de cambio de tier

---

### 3.3 Infraestructura de almacenamiento

- `storage_tier`: nivel de almacenamiento (hot, cool, archive)

---

### 3.4 Variables operativas

- `transfer_duration_sec`: duración de transferencia
- `transfer_speed_mbps`: velocidad de transferencia

⚠️ Variable derivada:

$$
transfer_{speed} = \frac{size}{tiempo}
$$

---

### 3.5 Variables de error (calidad del dato)

- `error_duplicado`
- `error_orphan`
- `error_null`
- `error_blob_timeout`

Modelo:

$$
error \in \{0,1\}
$$

---

### 3.6 Variables derivadas de contenido (hash)

- `hash_head`
- `hash_tail`

Representación:

$$
hash_{head} = H(content)_{[0:k]}
$$

$$
hash_{tail} = H(content)_{[-k:]}
$$

Estas variables funcionan como:

- proxy probabilístico de persistencia
- indicador de recurrencia del contenido
- aproximación a detección de duplicados

⚠️ Alta cardinalidad  
⚠️ No interpretables directamente  

---

## 4. Uso en el Modelo

Las variables independientes son utilizadas para explicar:

$$
Y = storage_{cost}
$$

y en general:

$$
Y = f(X)
$$

donde:

$$
X = \{size_{gb}, file_{type}, storage_{tier}, days_{stored}, error, hash\}
$$

---

## 5. Consideración Crítica

El modelo no busca reproducir exactamente la distribución real del sistema, sino construir un entorno controlado que permita:

- evaluar relaciones causales entre variables
- identificar patrones estructurales
- analizar impacto en costo

---

## 6. Principio Fundamental

> Las variables independientes representan los factores que generan el comportamiento del sistema, mientras que el costo y las métricas son consecuencias.

---

## 7. Relación con las 5Vs

- Volume → size_gb  
- Velocity → transferencia  
- Variety → file_type  
- Veracity → errores  
- Value → emerge del modelo  

---

## 8. Conclusión

Las variables independientes permiten construir un modelo donde:

- el comportamiento del sistema se explica desde el archivo
- el costo es una consecuencia
- las anomalías son variables explicativas

---

## 🔁 Navegación de retorno

⬅️ [Volver al Dataset](03_estructura_dataset.md)  
➡️ [Ir a Variables Dependientes (Y)](03_02_variables_dependientes.md)
