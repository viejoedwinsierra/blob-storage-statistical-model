---
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---

# 3. Estructura y generación del dataset

## 3.1 Objetivo

Construir un dataset estructurado que represente el inventario de archivos almacenados en un sistema de object storage durante un horizonte de 180 días, incorporando tanto operación normal como eventos de falla.

El dataset se orienta al análisis del **costo de almacenamiento y ciclo de vida del dato**, sirviendo como base para:

* Modelado estadístico del costo
* Análisis exploratorio de datos (EDA)
* Entrenamiento de modelos de machine learning
* Evaluación del ciclo de vida del almacenamiento
* Identificación de anomalías

---

## 3.2 Definición formal de la unidad de análisis

Se define el dataset como:

$$
\mathcal{D} = \{u_i\}_{i=1}^{M_d}
$$

donde cada instancia $u_i$ corresponde a un **archivo almacenado (blob)**.

Cada registro representa:

* un archivo físico
* atributos de tamaño y tipo
* variables temporales
* condiciones operativas
* variables de error asociadas

---

## 3.3 Fuente de datos

El dataset puede construirse bajo dos esquemas:

### 3.3.1 Dataset real

Derivado de Blob Storage mediante:

* enumeración de blobs
* extracción de metadatos físicos

### 3.3.2 Dataset simulado

Generado mediante un modelo probabilístico controlado que replica:

* generación de archivos
* distribución de tamaños
* asignación de almacenamiento
* inyección de errores

---


## 3.4.1 Definición de variables del dataset

A partir de la estructura definida, se establecen las siguientes variables, clasificadas según su tipo de dato, tipo estadístico y rol dentro del modelo.

### Tabla: Definición de variables

| Variable | Tipo de dato | Tipo de variable | Clasificación | Rol | Descripción |
|----------|-------------|------------------|--------------|-----|-------------|
| file_id | Texto | Identificador | - | Técnica | Identificador único del archivo |
| file_type | Categórica | Nominal | X | Explicativa | Tipo de archivo (json, jpg, pdf, mp4) |
| size_gb | Numérica | Cuantitativa continua | X | Clave | Tamaño del archivo en GB |
| storage_tier | Categórica | Nominal | X | Clave | Nivel de almacenamiento (hot, cool, archive) |
| days_stored | Numérica | Cuantitativa discreta | X | Clave | Tiempo de almacenamiento en días |
| days_since_last_access | Numérica | Cuantitativa discreta | X | Explicativa | Días desde último acceso |
| read_level | Categórica | Ordinal | X | Explicativa | Nivel de lectura (low, medium, high) |
| modify_level | Categórica | Ordinal | X | Explicativa | Nivel de modificación |
| movement_storage | Numérica (0/1) | Binaria (dicotómica) | X | Explicativa | Indica cambio de tier |
| transfer_duration_sec | Numérica | Cuantitativa continua | X | Explicativa | Duración de transferencia |
| transfer_speed_mbps | Numérica | Cuantitativa continua | X | Explicativa | Velocidad de transferencia |
| hash_head | Texto | Cualitativa nominal | X | Explicativa ⚠️ | Segmento inicial del hash |
| hash_tail | Texto | Cualitativa nominal | X | Explicativa ⚠️ | Segmento final del hash |
| error_duplicado | Numérica (0/1) | Binaria (dicotómica) | X | Secundaria | Duplicidad lógica |
| error_orphan | Numérica (0/1) | Binaria (dicotómica) | X | Secundaria | Inconsistencia de archivos |
| error_null | Numérica (0/1) | Binaria (dicotómica) | X | Secundaria | Registro sin contenido |
| error_blob_timeout | Numérica (0/1) | Binaria (dicotómica) | X | Secundaria | Error operativo |
| storage_cost | Numérica | Cuantitativa continua | Y | Dependiente | Costo de almacenamiento |

---

### Clasificación general del modelo

#### Variable dependiente (Y)

- storage_cost

#### Variables independientes (X)

**Cuantitativas:**
- size_gb
- days_stored
- days_since_last_access
- transfer_duration_sec
- transfer_speed_mbps

**Categóricas:**
- file_type
- storage_tier
- read_level
- modify_level

**Binarias:**
- movement_storage
- error_duplicado
- error_orphan
- error_null
- error_blob_timeout

---

### Observaciones metodológicas

- Se eliminan variables redundantes (ej: tamaño en MB vs GB).
- Se identifica relación estructural entre variables:

$$
storage\_cost = size_{gb} \cdot tarifa_{tier} \cdot \frac{days_{stored}}{30}
$$

- La variable `transfer_speed_mbps` es derivada de tamaño y tiempo, lo que puede introducir dependencia.

- Las variables `hash_head` y `hash_tail` presentan alta cardinalidad ⚠️, por lo que requieren transformación antes de su uso en modelos estadísticos.

- Las variables de error son consideradas explicativas secundarias, orientadas a capturar anomalías del sistema.

---


## 3.5 Tipología de archivos

Se limita el sistema a cuatro tipos principales:

| Tipo | Uso |
|------|----|
| JSON | Metadatos |
| JPG | Imágenes |
| PDF | Documentos |
| MP4 | Multimedia |

Esta restricción permite controlar la variabilidad y facilitar el análisis estadístico.

---

## 3.6 Modelo de costo de almacenamiento

El costo se define como:

$$
storage\_cost = size_{gb} \cdot tarifa_{tier} \cdot \frac{days\_stored}{30}
$$

donde:

* $size_{gb}$: tamaño del archivo  
* $tarifa_{tier}$: costo por nivel de almacenamiento  
* $days_{stored}$: tiempo almacenado  

---

## 3.7 Variables de error

Los errores se modelan como variables binarias:

$$
error \in \{0,1\}
$$

Esto permite:

* evitar duplicidad de registros
* representar múltiples condiciones de error
* mejorar interpretabilidad del modelo

---

## 3.8 Variables derivadas de contenido

Se incorporan variables basadas en el hash del contenido:

* `hash_head`
* `hash_tail`

Estas permiten:

* detección de duplicados
* análisis de similitud
* trazabilidad del contenido

⚠️ Estas variables presentan alta cardinalidad y requieren transformación para su uso en modelos.

---

## 3.9 Relación estructural del modelo

El modelo de costo sigue la relación:

$$
storage\_cost \approx f(size_{gb}, days_{stored}, storage_{tier})
$$

Adicionalmente:

$$
transfer\_speed \approx \frac{size}{tiempo}
$$

Esto introduce posibles dependencias estructurales entre variables.

---

## 3.10 Consideraciones metodológicas

* Eliminación de variables redundantes (ej: MB vs GB)
* Separación entre variables estructurales y de error
* Inclusión de variables de ciclo de vida del dato
* Uso de variables categóricas, cuantitativas y binarias

---

## 3.11 Uso en machine learning

El dataset permite modelar:

$$
Y = storage\_cost
$$

donde:

* $Y$: costo de almacenamiento  
* $X$: variables del archivo, tiempo, tipo y errores  

Aplicaciones:

* modelos de regresión
* análisis de importancia de variables
* detección de anomalías
* optimización de almacenamiento

---

## 3.12 Consideraciones finales

El dataset representa una aproximación controlada del sistema de almacenamiento, permitiendo:

* análisis reproducible
* validación de hipótesis
* entrenamiento de modelos
* integración entre estadística, probabilidad y machine learning

La calidad del análisis dependerá de:

* correcta selección de variables  
* control de dependencias  
* tratamiento de variables de alta cardinalidad ⚠️  
