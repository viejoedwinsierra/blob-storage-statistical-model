---
🏠 [Inicio](../README.md)

⬅️ [Dataset](03_estructura_dataset.md)

⬅️ [Variables Independientes](03_01_variables_independientes.md)

➡️ [Parámetros de Simulación](04_parametros_simulacion.md)
---

# Variables Dependientes del Sistema

## 1. Definición

Las variables dependientes representan los resultados observables a nivel de archivo dentro del sistema de almacenamiento.

A diferencia de un enfoque agregado por tiempo, este modelo define variables dependientes a nivel de **instancia (archivo)**, permitiendo:

- análisis estadístico directo
- modelamiento con machine learning
- construcción posterior de métricas agregadas

---

## 2. Principio de Modelado

El modelo se construye a nivel micro (archivo), donde:

- cada observación representa una unidad de almacenamiento
- las variables dependientes capturan el resultado del comportamiento del sistema sobre cada archivo

Por lo tanto:

> Las variables agregadas del sistema (volumen total, tasas, costos globales) se derivan a partir de las variables a nivel archivo.

---

## 3. Variables Dependientes Definidas

### 3.1 Costo de almacenamiento

- `storage_cost`: costo individual del archivo

Representa:

- impacto económico por archivo
- efecto combinado de tamaño, tiempo y tipo de almacenamiento

---

### 3.2 Estado de calidad del archivo

- `is_duplicate`: indicador de duplicidad derivado de hash o error
- `has_error`: indicador general de error
- `is_orphan`: inconsistencia, por ejemplo JSON sin PDF

Estas variables permiten modelar:

- veracidad del dato
- calidad del sistema

---

### 3.3 Estado operativo

- `is_moved`: indica si el archivo fue movido entre tiers
- `is_active`: indica si el archivo está en uso, derivable de acceso

Estas variables representan el comportamiento del archivo dentro del ciclo de vida.

---

## 4. Variables derivadas a nivel sistema

A partir de las variables anteriores se pueden construir métricas agregadas.

### 4.1 Volumen

$$
V_{total} = \sum size_{gb}
$$

$$
V_{useful}
$$

$$
V_{redundant}
$$

---

### 4.2 Calidad

- `error_rate`
- `dup_rate`
- `inconsistency_rate`

---

### 4.3 Costo

$$
C_{total} = \sum storage_{cost}
$$

---

### 4.4 Estado del sistema

$$
N_t = \text{número de archivos}
$$

$$
D_t = \text{duplicados}
$$

$$
U_t = \text{almacenamiento total}
$$

---

## 5. Uso en el modelo

Las variables dependientes permiten:

- entrenar modelos de regresión para costo
- entrenar modelos de clasificación para errores y duplicados
- construir métricas agregadas del sistema
- analizar eficiencia del almacenamiento

---

## 6. Relación con el almacenamiento

El almacenamiento se modela a nivel de archivo:

- cada archivo contribuye al volumen total
- cada archivo genera costo
- cada archivo puede contener errores

El comportamiento global emerge de la agregación.

---

## 7. Relación con el ciclo de vida

El ciclo de vida del dato se observa en cada archivo:

- creación
- almacenamiento
- acceso
- modificación
- posible movimiento

Las variables dependientes capturan el resultado de este proceso.

---

## 8. Relación con las 5Vs

A nivel archivo:

- Volume → `size_gb`
- Velocity → transferencia
- Variety → `file_type`
- Veracity → errores
- Value → `storage_cost`

A nivel agregado:

- Volume → suma de tamaños
- Value → costo total

---

## 9. Principio clave

> El sistema no se modela directamente: se modelan sus componentes (archivos), y el comportamiento global emerge de su agregación.

---

## 10. Conclusión

Las variables dependientes permiten:

- modelar el sistema desde una perspectiva micro
- construir métricas agregadas coherentes
- integrar análisis estadístico y machine learning

Esto garantiza consistencia entre dataset, modelo y análisis.

---

🏠 [Inicio](../README.md)

⬅️ [Dataset](03_estructura_dataset.md)

⬅️ [Variables Independientes](03_01_variables_independientes.md)

➡️ [Parámetros de Simulación](04_parametros_simulacion.md)
