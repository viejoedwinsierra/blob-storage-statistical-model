🏠 [Inicio](../README.md)
⬅️ [Anterior](02_procedencia_fuente.md)
➡️ [Siguiente](04_parametros_simulacion.md)

---

# 3.4 Estructura del dataset y definición de variables

## 🎯 Introducción

El dataset utilizado en este estudio es generado mediante un simulador parametrizable basado en Monte Carlo:

🔗 https://github.com/viejoedwinsierra/mvp_thesis_simulator

Este dataset representa el comportamiento de un sistema de almacenamiento tipo Blob Storage a nivel de archivo, permitiendo analizar:

* costo de almacenamiento
* eficiencia de transferencia
* ocurrencia de errores
* patrones de uso del dato

---

## 🧩 Vista conceptual del dataset

> 📌 **Abrebocas visual del sistema modelado**

![Arquitectura del dataset](../img/dataset_overview.png)

**Figura 1.** Representación conceptual del dataset generado por el simulador.

El dataset se construye como una colección de observaciones independientes donde cada fila representa un archivo con:

* características físicas
* variables temporales
* condiciones operativas
* variables derivadas

---

## 🔄 Relación con el simulador

El dataset no es estático: es el resultado de múltiples ejecuciones del simulador.

```text
Simulador (configurable)
        ↓
Generación de variables aleatorias
        ↓
Construcción de dataset
        ↓
Modelamiento estadístico
```

Cada ejecución puede generar:

* diferentes distribuciones
* distintos escenarios
* variabilidad controlada

Esto permite evaluar **robustez del modelo**.

---

## 📊 Tabla: Definición completa de variables

| Variable               | Tipo de dato | Tipo de variable | Clasificación | Rol            | Descripción                           |
| ---------------------- | ------------ | ---------------- | ------------- | -------------- | ------------------------------------- |
| file_id                | Texto        | Identificador    | -             | Técnica        | Identificador único del archivo       |
| file_type              | Categórica   | Nominal          | X             | Explicativa    | Tipo de archivo (json, jpg, pdf, mp4) |
| size_gb                | Numérica     | Continua         | X             | Clave          | Tamaño del archivo                    |
| storage_tier           | Categórica   | Nominal          | X             | Clave          | Nivel de almacenamiento               |
| days_stored            | Numérica     | Discreta         | X             | Clave          | Tiempo en almacenamiento              |
| days_since_last_access | Numérica     | Discreta         | X             | Explicativa    | Último acceso                         |
| read_level             | Categórica   | Ordinal          | X             | Explicativa    | Nivel de lectura                      |
| modify_level           | Categórica   | Ordinal          | X             | Explicativa    | Nivel de modificación                 |
| movement_storage       | Binaria      | Discreta         | X             | Explicativa    | Cambio de tier                        |
| transfer_duration_sec  | Numérica     | Continua         | X             | Explicativa    | Tiempo de transferencia               |
| transfer_speed_mbps    | Numérica     | Continua         | X             | Derivada ⚠️    | Calculada                             |
| hash_head              | Texto        | Nominal          | X             | Explicativa ⚠️ | Proxy de contenido                    |
| hash_tail              | Texto        | Nominal          | X             | Explicativa ⚠️ | Proxy de contenido                    |
| error_duplicado        | Binaria      | Discreta         | X             | Secundaria     | Error lógico                          |
| error_orphan           | Binaria      | Discreta         | X             | Secundaria     | Inconsistencia                        |
| error_null             | Binaria      | Discreta         | X             | Secundaria     | Registro vacío                        |
| error_blob_timeout     | Binaria      | Discreta         | X             | Secundaria     | Error operativo                       |
| storage_cost           | Numérica     | Continua         | Y             | Dependiente    | Costo de almacenamiento               |

---

## 📈 Relaciones clave entre variables

> 📌 **Relaciones estructurales del dataset**

![Relaciones del modelo](../img/variable_relationships.png)

**Figura 2.** Relaciones funcionales entre variables del dataset.

Algunas relaciones importantes:

### 1. Costo de almacenamiento

$$
storage_{cost} =
size_{gb} \cdot tarifa_{tier} \cdot \frac{days_{stored}}{30}
$$

👉 Relación determinística base del sistema.

---

### 2. Velocidad de transferencia

$$
transfer_{speed} = \frac{size_{gb}}{transfer_{duration}}
$$

👉 Variable derivada (no independiente).

---

## 🧠 Clasificación formal del modelo

### Variable dependiente

$$
Y = storage_{cost}
$$

---

### Variables independientes

$$
X = {
size_{gb},
file_{type},
storage_{tier},
days_{stored},
error_i,
hash_{head},
hash_{tail}
}
$$

---

### Relación del modelo

$$
Y = f(X)
$$

$$
P(Y \mid X)
$$

---

## ⚠️ Consideraciones metodológicas

### 1. Variables derivadas

* `transfer_speed` depende de otras variables
* No debe usarse sin control en modelos

---

### 2. Alta cardinalidad

* `hash_head` y `hash_tail`
* Pueden generar sobreajuste

---

### 3. Variables binarias de error

* Representan calidad del sistema
* Pueden tener baja frecuencia

---

### 4. Dependencia del simulador

El dataset depende directamente de:

🔗 https://github.com/viejoedwinsierra/mvp_thesis_simulator

Esto implica que:

* cambios en parámetros → cambian distribuciones
* resultados deben interpretarse bajo ese contexto

---

## 🔗 Navegación del modelo

⬅️ [Volver al Dataset](03_estructura_dataset.md)

➡️ [Variables independientes (X)](03_01_variables_independientes.md)
➡️ [Variables dependientes (Y)](03_02_variables_dependientes.md)

