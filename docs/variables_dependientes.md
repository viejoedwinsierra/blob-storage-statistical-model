# Variables Dependientes del Sistema

## 1. Definición

Las variables dependientes representan los resultados observables del sistema, es decir, aquellas magnitudes que emergen a partir de la interacción de:

- la demanda
- los errores
- el almacenamiento
- el ciclo de vida de los datos

Estas variables son fundamentales para medir:

- eficiencia
- calidad
- costo
- comportamiento del sistema

---

## 2. Principio de Modelado

A diferencia de las variables independientes, las variables dependientes:

- evolucionan en el tiempo
- reflejan el estado real del sistema
- permiten validar hipótesis

---

## 3. Variables Dependientes Definidas

### 3.1 Volumen

- V_total(t): volumen total almacenado
- V_useful(t): volumen útil
- V_redundant(t): volumen redundante

---

### 3.2 Calidad (Veracity)

- error_rate: tasa de error observada
- dup_rate: tasa de duplicados
- inconsistency_rate: inconsistencias (ej. JSON sin PDF)
- Veracity_index: índice compuesto de calidad

---

### 3.3 Velocidad

- λ_obs(t): tasa real de ingestión
- retry_rate: tasa de reintentos
- throughput: volumen procesado por tiempo
- latencia observada

---

### 3.4 Valor

- C_total: costo total del sistema
- C_storage: costo de almacenamiento
- C_error: costo inducido por errores
- Value_index: proporción de datos utilizables

---

### 3.5 Estado del sistema

- N_t: número total de objetos
- D_t: duplicados acumulados
- J_t: costo acumulado
- U_t: almacenamiento utilizado

---

## 4. Uso en la Simulación

Las variables dependientes permiten:

- evaluar la eficiencia del sistema
- medir el impacto de errores
- calcular el costo total
- analizar el comportamiento dinámico

---

## 5. Rol dentro del modelo

Las variables dependientes no buscan describir el dato en sí, sino el sistema:

> El modelo no busca similitudes físicas o lógicas entre archivos, sino medir cómo el sistema gestiona dichos archivos.

Esto implica que:

- no se modela el contenido semántico del archivo
- se modela su comportamiento dentro del sistema

---

## 6. Relación con el almacenamiento

El almacenamiento no es estático.

El movimiento de los datos (ingestión, duplicación, retención, eliminación) define:

- el crecimiento del volumen
- la aparición de redundancia
- la eficiencia del sistema

---

## 7. Relación con el ciclo de vida

El ciclo de vida de los datos garantiza que:

- los datos se generan
- se almacenan
- se procesan
- se consumen
- se eliminan

Las variables dependientes capturan el efecto de este ciclo.

---

## 8. Relación con las 5Vs

Las variables dependientes son la manifestación directa de las 5Vs:

- Volume → V_total, V_useful
- Velocity → λ_obs, throughput
- Variety → distribución de tipos
- Veracity → error_rate, dup_rate
- Value → costo, eficiencia

---

## 9. Principio clave

> El valor del modelo no está en los datos, sino en la capacidad de medir el comportamiento del sistema frente a los datos.

---

## 10. Conclusión

Las variables dependientes permiten:

- cuantificar el impacto de las decisiones
- evaluar la eficiencia del almacenamiento
- medir la calidad del sistema
- validar hipótesis sobre costo y rendimiento

Son el núcleo del análisis del sistema.
