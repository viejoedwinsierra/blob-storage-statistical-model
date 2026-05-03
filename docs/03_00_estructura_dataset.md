---
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---

# 3. Estructura y generación del dataset

## 🔗 Navegación del modelo

Este dataset constituye la base del modelo estadístico.  
Puedes navegar a la definición formal de variables aquí:

➡️ [Variables independientes (X)](03_01_variables_independientes.md)  
➡️ [Variables dependientes (Y)](03_02_variables_dependientes.md)

---

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

## 3.4 Definición de variables del dataset

📌 Para detalle completo de variables:

➡️ [Ir a Variables independientes (X)](03_01_variables_independientes.md)  
➡️ [Ir a Variables dependientes (Y)](03_02_variables_dependientes.md)

---

### Tabla: Definición de variables

| Variable | Tipo | Rol |
|----------|------|-----|
| file_type | Categórica | X |
| size_gb | Numérica | X |
| storage_tier | Categórica | X |
| days_stored | Numérica | X |
| hash_head | Texto ⚠️ | X |
| hash_tail | Texto ⚠️ | X |
| error_* | Binaria | X |
| storage_cost | Numérica | Y |

---

## 🔄 Relación del dataset con el modelo

El dataset permite modelar la relación:

$$
Y = f(X)
$$

donde:

$$
X = \{ size_{gb}, file_{type}, storage_{tier}, days_{stored}, error, hash \}
$$

$$
Y = \{ storage_{cost} \}
$$

De forma probabilística:

$$
P(Y \mid X)
$$

---

## 🔗 Navegación del modelo

Puedes profundizar en las variables:

➡️ [Variables independientes (X)](03_01_variables_independientes.md)  
➡️ [Variables dependientes (Y)](03_02_variables_dependientes.md)

---

## 3.5 Tipología de archivos

Se limita el sistema a cuatro tipos principales:

| Tipo | Uso |
|------|----|
| JSON | Metadatos |
| JPG | Imágenes |
| PDF | Documentos |
| MP4 | Multimedia |

---

## 3.6 Modelo de costo de almacenamiento

$$
storage_{cost} =
size_{gb} \cdot tarifa_{tier} \cdot \frac{days_{stored}}{30}
$$

---

## 3.7 Variables de error

$$
error \in \{0,1\}
$$

---

## 3.8 Variables derivadas de contenido

Variables utilizadas:

- `hash_head`
- `hash_tail`

Estas permiten:

- detección de duplicados
- análisis de persistencia
- identificación de recurrencia

⚠️ Alta cardinalidad

---

## 3.9 Relación estructural del modelo

$$
storage_{cost} \approx f(size_{gb}, days_{stored}, storage_{tier})
$$

$$
transfer_{speed} \approx \frac{size}{tiempo}
$$

---

## 3.10 Consideraciones metodológicas

- Eliminación de redundancia
- Separación de variables
- Control de dependencias
- Uso de variables binarias

---

## 🔁 Navegación de retorno

Una vez revisadas las variables, puedes volver aquí:

⬅️ [Volver al dataset](03_estructura_dataset.md)

---

## 3.11 Uso en machine learning

$$
Y = storage_{cost}
$$

Aplicaciones:

- regresión
- detección de anomalías
- optimización de almacenamiento

---

## 3.12 Consideraciones finales

El dataset permite:

- análisis reproducible
- validación de hipótesis
- entrenamiento de modelos

---
