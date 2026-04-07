# 📦 Blob Storage Statistical Model

Marco metodológico reproducible para analizar, simular y optimizar sistemas de almacenamiento tipo **AWS S3 / Azure Blob Storage**.

---

## 🧩 Naturaleza del proyecto

Este repositorio presenta un modelo integrado que combina:

- simulación de datos (Airflow)
- modelamiento estadístico
- análisis de comportamiento del sistema
- evaluación de costos en entornos cloud

El proyecto no solo describe datos, sino que **modela dinámicamente un sistema de almacenamiento distribuido**, permitiendo analizar su evolución bajo diferentes condiciones.

---

## ☁️ Contexto de aplicación

El modelo se implementa sobre arquitecturas tipo:

- AWS (S3, Glue, Athena, Lambda)
- Azure (Blob Storage, Data Factory, Synapse)

permitiendo evaluar el impacto del comportamiento del sistema en el costo y la eficiencia operativa.

---

## ⚙️ Arquitectura

Simulación (Airflow DAG) → Storage (S3/Blob) → Procesamiento → Analytics → Decisiones

---

## 🔁 Simulación con Airflow

- Generación de datos sintéticos  
- Inyección de fallas  
- Duplicación de contenido  
- Ejecución temporal controlada  
---

## ☁️ AWS vs Azure (Comparación de arquitectura)

El proyecto puede implementarse tanto en AWS como en Azure, utilizando servicios equivalentes en cada capa del pipeline.

### 🔹 Comparación por componentes

| Capa | AWS | Azure | Rol en el proyecto |
|------|-----|------|-------------------|
| Storage | S3 | Blob Storage | Almacenamiento de archivos simulados |
| Procesamiento (ETL) | Glue | Data Factory | Transformación y limpieza de datos |
| Consulta | Athena | Synapse | Análisis y consultas sobre datos |
| Serverless | Lambda | Azure Functions | Procesamiento por eventos (validación, duplicados) |
| Orquestación | Airflow / Step Functions | Airflow / Data Factory | Ejecución del pipeline |
| Machine Learning | SageMaker | Azure ML | Modelos predictivos |
| Monitoreo | CloudWatch | Azure Monitor | Métricas y observabilidad |

---

### 🔹 Comparación funcional

| Criterio | AWS | Azure |
|---------|-----|------|
| Enfoque | Serverless nativo | Integración empresarial |
| Consulta de datos | Athena (pago por consulta) | Synapse (modelo híbrido) |
| Integración | Amplia con servicios cloud | Fuerte integración con ecosistema Microsoft |
| Facilidad de uso | Alta para analytics | Alta para entornos corporativos |
| Event-driven | Muy maduro (S3 + Lambda) | Muy sólido (Blob + Functions) |

---

### 🔹 Comparación de costos (conceptual)

| Componente | AWS | Azure |
|-----------|-----|------|
| Storage | Pago por GB almacenado (S3) | Pago por GB almacenado (Blob) |
| Requests | Costo por operaciones (PUT, GET) | Costo por operaciones |
| Procesamiento | Glue / Athena por uso | Data Factory / Synapse por uso |
| Serverless | Lambda por ejecución | Functions por ejecución |

---

### 🔹 Interpretación en el proyecto

- Ambos entornos permiten implementar el mismo modelo de simulación y análisis.  
- AWS facilita arquitecturas serverless altamente desacopladas.  
- Azure facilita integración con entornos empresariales y analítica unificada.  

👉 La comparación permite evaluar:

- impacto en costos  
- facilidad de implementación  
- comportamiento del sistema bajo diferentes arquitecturas  

---

## 🧭 Guía de lectura

La documentación está organizada siguiendo el flujo lógico del problema:

### 🔹 1. Contexto y datos
(Secciones 01–03)

Define el problema, la fuente de datos y la estructura del sistema.

---

### 🔹 2. Simulación del sistema
(Sección 04)

Describe cómo se generan los datos mediante un modelo controlado y reproducible (Airflow).

---

### 🔹 3. Modelamiento y análisis
(Secciones 05–06)

Aplica técnicas estadísticas para analizar el comportamiento del almacenamiento.

---

### 🔹 4. Validación y limitaciones
(Secciones 07–08)

Evalúa la calidad del modelo y sus restricciones.

---

### 🔹 5. Análisis causal
(Sección 09)

Explica cómo las fallas impactan el sistema y generan anomalías.

---

### 🔹 6. Soporte teórico
(Sección 10)

Incluye referencias y fundamentos utilizados.

---

## 📁 Entregable principal 

Haz clic en cada sección: 
1. [01 - Contexto del problema](docs/01_contexto_problema.md) 
2. [02 - Procedencia y fuente](docs/02_procedencia_fuente.md) 
3. [03 - Estructura del dataset](docs/03_estructura_dataset.md) 
4. [04 - Parámetros de simulación](docs/04_parametros_simulacion.md) 
5. [05 - Modelamiento estadístico](docs/05_modelamiento_estadistico.md) 
6. [06 - Preguntas analíticas](docs/06_preguntas_analiticas.md) 
7. [07 - Limitaciones metodológicas](docs/07_limitaciones_metodologia.md) 
8. [08 - Validaciones de calidad](docs/08_validaciones_calidad.md) 
9. [09 - Modelo causal de fallas](docs/09_modelo_causal_fallas.md) 
10. [10 - Referencias](docs/10_referencias.md) 

---

## 🎓 Aplicación de las materias en el proyecto

Este proyecto integra conocimientos de cuatro áreas fundamentales, aplicados de manera conjunta para modelar y analizar sistemas de almacenamiento en la nube.

---

### 📊 Modelos Estadísticos

Se aplican modelos de conteo y análisis temporal para representar el crecimiento del almacenamiento y la ocurrencia de eventos en el sistema.

- Uso de distribuciones (Poisson) para modelar llegada de archivos  
- Análisis de crecimiento del almacenamiento en el tiempo  
- Evaluación de escenarios normales y con fallas  

👉 Permite cuantificar el comportamiento del sistema y validar hipótesis.

---

### 🎲 Probabilidad

Se modela la incertidumbre inherente al sistema mediante variables aleatorias que representan la generación de datos y la ocurrencia de fallas.

- Probabilidad de error en el pipeline (p_fail)  
- Variabilidad en la carga del sistema  
- Simulación de eventos aleatorios  

👉 Permite representar condiciones reales de operación bajo incertidumbre.

---

### 🤖 Machine Learning

Se incorporan modelos predictivos para estimar el comportamiento futuro del sistema y apoyar la toma de decisiones.

- Predicción del crecimiento del almacenamiento  
- Estimación de costos futuros  
- Identificación de patrones y anomalías  

👉 Permite pasar de análisis descriptivo a decisiones basadas en datos.

---

### 📊 Big Data e Ingeniería de Datos

Se implementa una arquitectura de pipeline basada en principios modernos de procesamiento de datos.

- Uso de Data Lake (S3 / Blob Storage)  
- Orquestación con Apache Airflow  
- Procesamiento y consulta de datos (Athena / Synapse)  

👉 Permite manejar grandes volúmenes de datos de forma escalable y reproducible.

---

## 💰 Modelo de costos

El costo total del sistema no depende únicamente del almacenamiento, sino del comportamiento integral del pipeline.

### Componentes principales

- **Storage**: volumen almacenado en S3 o Blob Storage
- **Requests**: operaciones PUT, GET, LIST y validaciones
- **Transferencia**: salida de datos y consumo entre servicios
- **Procesamiento**: ejecución de ETL, consultas y análisis
- **Serverless**: invocaciones de AWS Lambda o Azure Functions

### Formulación general

$$
C_t = C_{storage}(t) + C_{requests}(t) + C_{transfer}(t) + C_{processing}(t) + C_{serverless}(t)
$$

donde:

- $$
C_{storage}(t) = storage_{GB}(t)\cdot price_{GB}
$$

- $$
C_{requests}(t) = n_{put}(t)\cdot c_{put} + n_{get}(t)\cdot c_{get} + n_{list}(t)\cdot c_{list}
$$

- $$
C_{transfer}(t) = egress_{GB}(t)\cdot c_{egress}
$$

- $$
C_{processing}(t) = jobs_t\cdot c_{job} + query\_scan_{TB}(t)\cdot c_{scan}
$$

- $$
C_{serverless}(t) = invocations_t\cdot c_{invoke} + duration_t\cdot memory_t\cdot c_{compute}
$$

### Interpretación

Este modelo permite cuantificar cómo las decisiones técnicas impactan el costo total. Por ejemplo:

- un mayor volumen de archivos incrementa el costo de storage
- una mayor tasa de fallas incrementa requests, reintentos y procesamiento
- una estrategia deficiente de lifecycle incrementa el costo acumulado

### Costo acumulado

$$
J = \sum_{t=1}^{T} C_t
$$

Este valor representa el costo total del sistema durante el horizonte de simulación.

---

## 📊 Parámetros del modelo

### 🔹 Nivel base (académico)

- N = 10,000  
- 180 días  
- λ = 1000 blobs/día  
- k = 3  
- p_ok = 0.02  
- p_fail = 0.10  

---

### 🔹 Nivel avanzado (dinámico)

El modelo evoluciona hacia un sistema dependiente del tiempo:

- λ(t): tasa variable de generación  
- p_fail(t): probabilidad de falla dependiente de carga  
- storage(t): crecimiento dinámico  
- C(t): costo dependiente del uso  

---

Esto permite simular condiciones reales de operación en sistemas cloud.
 
---

## 🚀 Valor

- Simulación realista  
- Evaluación de costos  
- Base para decisiones  

