# Variables Independientes del Sistema

## 1. Definición

Las variables independientes corresponden a los factores exógenos del sistema, es decir, aquellos que no son controlados por la infraestructura de almacenamiento, pero que determinan su comportamiento.

Estas variables representan:

- La dinámica del negocio
- El comportamiento del sistema distribuido
- Las características del flujo de datos
- Las políticas del ciclo de vida

---

## 2. Principio de Modelado

El objetivo del modelo no es replicar fielmente la realidad física del sistema, sino capturar las relaciones funcionales que afectan:

- la eficiencia del almacenamiento
- la calidad de los datos (veracidad)
- el costo del sistema
- el comportamiento dinámico del ciclo de vida

Por lo tanto:

> Las variables independientes no buscan reproducir el universo real de datos, sino generar un entorno controlado donde se puedan analizar patrones estructurales del sistema.

---

## 3. Variables Independientes Definidas

### 3.1 Demanda y generación

- λ(t): tasa de generación de archivos
- Distribución temporal (por hora/día)
- Número de contenidos únicos

### 3.2 Características de los datos

- size_distribution: tamaño de archivos
- data_type: tipo de dato (pdf, json, etc.)
- relaciones entre archivos (ej. JSON ↔ PDF)

### 3.3 Sistema distribuido

- p_failure: probabilidad de fallo
- retry_policy: política de reintento
- concurrency_level: nivel de concurrencia
- latency: latencia de red

### 3.4 Ciclo de vida

- retention_days: tiempo de retención
- cleanup_policy: política de limpieza
- lifecycle_stage: etapa del ciclo de vida

### 3.5 Infraestructura

- infra_type: {on_prem, azure_blob, aws_s3}
- modelo de costos (storage, requests, transfer)

---

## 4. Uso en la Simulación

Las variables independientes son utilizadas para:

- generar el dataset sintético
- introducir condiciones de error
- modelar escenarios de alta carga
- simular comportamientos reales (retries, duplicados, inconsistencias)

---

## 5. Consideración Crítica

El modelo NO depende de la distribución exacta del universo real de datos.

Esto implica que:

- No es necesario conocer la distribución real completa
- Se pueden usar distribuciones controladas (normal, log-normal, uniforme)
- El foco está en el comportamiento emergente del sistema

---

## 6. Principio Fundamental

> La validez del modelo no depende de replicar datos reales, sino de capturar correctamente las relaciones entre generación, error, almacenamiento y costo.

---

## 7. Relación con las 5Vs

Las variables independientes definen indirectamente:

- Volume → a través de λ(t) y tamaño
- Velocity → a través de la distribución temporal
- Variety → a través de tipos de datos
- Veracity → a través de p_failure y retry
- Value → a través del control del ciclo de vida

---

## 8. Conclusión

Las variables independientes permiten construir un entorno abstracto y controlado que:

- desacopla el modelo del mundo físico real
- permite experimentar con distintos escenarios
- habilita el análisis del impacto en costo, eficiencia y calidad

Esto es clave para estudiar sistemas de almacenamiento a gran escala.
