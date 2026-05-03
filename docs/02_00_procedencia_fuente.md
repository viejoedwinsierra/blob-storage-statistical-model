---
🏠 [Inicio](../README.md)

⬅️ [Anterior](01_contexto_problema.md)

➡️ [Siguiente](02_01_rmarco_teorico_blobstorage.md)

---

# 2. Procedencia y fuente de la base de datos

Se emplea un enfoque de **simulación controlada** debido a restricciones de acceso a datos reales completos en entornos productivos. Este enfoque permite construir un entorno experimental reproducible donde las condiciones del sistema pueden ser parametrizadas y evaluadas de forma sistemática.

La base de datos se genera mediante un **modelo generativo estructurado**, diseñado para replicar el comportamiento de un sistema de almacenamiento de objetos a nivel de archivo, incorporando:

- características del dato (tamaño, tipo)
- ciclo de vida (tiempo, acceso)
- condiciones operativas (transferencia)
- variables de error
- estructuras de contenido (hash parcial)

---

## 2.1 Generación del universo de contenidos

Se define un conjunto inicial de archivos:

$$
N = 10{,}000
$$

Cada archivo representa una unidad observable del sistema y se caracteriza por:

* `file_id`: identificador único  
* `file_type`: tipo de archivo (json, jpg, pdf, mp4)  
* `size_gb`: tamaño del archivo  
* `creation_timestamp`: instante de creación  

El tamaño se modela como:

$$
size_{gb} \sim \text{LogNormal}(\mu, \sigma)
$$

Esta distribución captura:

- alta concentración de archivos pequeños  
- presencia de archivos grandes (cola larga)  

---

## 2.2 Generación de tipos de archivo

El tipo de archivo se modela como variable categórica:

$$
file_{type} \sim \text{Categorical}(p_{json}, p_{jpg}, p_{pdf}, p_{mp4})
$$

Esto permite capturar:

- variabilidad estructural del sistema  
- diferentes patrones de tamaño  
- impacto diferencial en almacenamiento  

---

## 2.3 Generación de ciclo de vida del dato

Se modela el tiempo de almacenamiento como:

$$
days_{stored} \sim F_t
$$

donde $F_t$ puede ser uniforme o exponencial.

Adicionalmente:

$$
days_{since\_last\_access} \sim \text{Uniform}(0, days_{stored})
$$

Esto permite representar:

- archivos activos  
- archivos fríos  
- comportamiento de acceso  

---

## 2.4 Asignación de nivel de almacenamiento

El nivel de almacenamiento se modela como:

$$
storage_{tier} \sim \text{Categorical}(\text{hot}, \text{cool}, \text{archive})
$$

Esta variable determina:

- costo unitario  
- comportamiento del sistema  
- eficiencia del almacenamiento  

---

## 2.5 Generación de variables operativas

Se modela la transferencia como:

$$
transfer_{duration} \sim F_d
$$

y la velocidad como variable derivada:

$$
transfer_{speed} = \frac{size_{gb}}{transfer_{duration}}
$$

Esto introduce:

- dependencia estructural entre variables  
- análisis de eficiencia  

---

## 2.6 Generación de errores

Los errores se modelan como variables binarias independientes:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

donde:

- $error_{duplicado}$  
- $error_{orphan}$  
- $error_{null}$  
- $error_{blob}$  

Esto permite capturar:

- condiciones de falla  
- calidad del sistema  
- veracidad del dato  

---

## 2.7 Representación parcial del contenido (hash)

Para evitar el costo computacional del procesamiento completo del contenido, se utilizan segmentos parciales:

$$
hash\_head = H(content)[0:k]
$$

$$
hash\_tail = H(content)[-k:]
$$

Estas variables funcionan como:

- proxy probabilístico de persistencia  
- indicador de recurrencia del contenido  
- aproximación a la detección de duplicados  

⚠️ Este enfoque reduce costo computacional pero introduce incertidumbre en la detección exacta.

---

## 2.8 Construcción del dataset maestro

Se define el dataset como:

$$
\mathcal{D} = \{u_i\}_{i=1}^{N}
$$

donde cada instancia $u_i$ contiene:

- variables de tamaño  
- variables de tipo  
- variables temporales  
- variables operativas  
- variables de error  
- variables de contenido (hash parcial)  

Este dataset se utiliza para:

- análisis exploratorio (EDA)  
- modelamiento estadístico  
- entrenamiento de modelos de machine learning  

---

## 2.9 Relación con el modelo probabilístico

El sistema se modela como:

$$
X = \{ size_{gb}, file_{type}, days_{stored}, storage_{tier}, error \}
$$

$$
Y = \{ storage_{cost}, has_{error}, is_{duplicate} \}
$$

y el objetivo es estimar:

$$
P(Y \mid X)
$$

---

## 2.10 Alcance analítico

El entorno permite analizar:

- distribución de tamaños  
- comportamiento del ciclo de vida  
- impacto de errores  
- patrones de duplicidad  
- relaciones entre variables  

---

## 2.11 Limitaciones del enfoque

El modelo presenta las siguientes limitaciones:

- uso de simulación en lugar de datos reales  
- simplificación de dependencias complejas  
- exclusión de optimizaciones internas (compresión, deduplicación física)  
- uso de hash parcial en lugar de hash completo ⚠️  

Por lo tanto, los resultados deben interpretarse como aproximaciones controladas.

---
