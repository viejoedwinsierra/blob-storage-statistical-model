---

🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---

# 1. Contexto del problema y propósito del análisis

En arquitecturas distribuidas basadas en microservicios, los sistemas de almacenamiento de objetos (*Object Storage*) constituyen la capa persistente donde se materializan los artefactos generados por procesos transaccionales y de integración. Estos sistemas operan bajo un modelo de crecimiento continuo impulsado por múltiples servicios desacoplados, lo que introduce dinámicas complejas de generación, replicación y almacenamiento de datos (Kleppmann, 2017).

Bajo condiciones normales de operación, el volumen de almacenamiento crece de manera aproximadamente proporcional al número de transacciones ejecutadas. Este comportamiento es consistente con modelos de procesamiento distribuido como *MapReduce* (Dean & Ghemawat, 2008), donde cada operación puede generar artefactos intermedios y finales que se almacenan de forma persistente.

Desde una perspectiva probabilística, la llegada de estos eventos puede modelarse mediante un proceso de Poisson, el cual describe la ocurrencia de eventos independientes en el tiempo (Ross, 2014). En este caso, cada transacción puede interpretarse como un evento que genera un artefacto documental, con un tamaño promedio cercano a 1 MB.

Sin embargo, esta aproximación se rompe en presencia de fallas operativas. Eventos como *timeouts*, reintentos automáticos, errores de idempotencia o inconsistencias en pipelines introducen comportamientos no lineales en la generación de artefactos. En estos escenarios, una misma transacción lógica puede generar múltiples archivos físicos, alterando la relación esperada entre eventos y almacenamiento (Beyer et al., 2016).

Este fenómeno puede formalizarse mediante un factor de amplificación ( k ), que modifica la tasa de generación de archivos:

$$
\lambda' = k \cdot \lambda
$$

donde ( \lambda ) representa la tasa de generación bajo condiciones normales. La ausencia de mecanismos adecuados de idempotencia —como se discute en (Kleppmann, 2017)— puede inducir duplicidad lógica, generando múltiples artefactos con contenido idéntico pero identificadores distintos.

Este tipo de duplicidad ha sido ampliamente estudiado en sistemas de almacenamiento mediante técnicas de deduplicación basadas en contenido (Meyer & Bolosky, 2011), donde se demuestra que el crecimiento redundante puede impactar significativamente la eficiencia del almacenamiento.

Desde un enfoque probabilístico, este comportamiento puede interpretarse como un proceso estocástico con cambio de régimen, en el cual el sistema alterna entre diferentes estados operativos. Estos modelos, conocidos como modelos de cambio de régimen, han sido ampliamente utilizados en análisis de series de tiempo para capturar dinámicas con múltiples estados latentes (Hamilton, 1994).

En este contexto, se identifican al menos dos regímenes:

* **Régimen normal**: baja tasa de duplicidad y crecimiento controlado
* **Régimen de incidente**: alta tasa de duplicidad y crecimiento acelerado

---

## Problema central

El problema fundamental no radica únicamente en el crecimiento del almacenamiento, sino en la **incertidumbre estructural sobre la relación entre eventos lógicos y artefactos físicos generados**. Esta incertidumbre afecta:

* la estimación del volumen real de almacenamiento requerido
* la detección temprana de anomalías operativas
* la eficiencia de mecanismos de deduplicación
* la capacidad de predecir comportamientos bajo carga o falla

Dado que los modelos de costo en plataformas cloud dependen directamente del volumen almacenado, este fenómeno impacta de forma directa la eficiencia económica del sistema (Microsoft Azure, 2023; Amazon Web Services, 2023).

---

## Enfoque del análisis

Debido a las limitaciones de acceso a datos reales completos y controlados en entornos productivos, se propone un enfoque basado en **simulación estructurada**. Este enfoque permite construir un dataset sintético que replica:

* la distribución temporal de eventos
* la generación de artefactos documentales
* la inyección controlada de errores operativos
* la aparición de duplicados lógicos
* la variabilidad en rutas y almacenamiento

Este dataset actúa como un entorno experimental controlado que permite analizar el comportamiento del sistema bajo diferentes configuraciones.

---

## Propósito del estudio

El propósito de este análisis es:

* Modelar estadísticamente la generación de archivos en sistemas distribuidos
* Cuantificar el impacto de la duplicidad en el consumo de almacenamiento
* Evaluar escenarios de crecimiento bajo distintos niveles de carga y falla
* Formular hipótesis contrastables sobre la dinámica del sistema
* Diseñar y validar modelos estadísticos y de *machine learning* para la detección de errores

En particular, se busca responder a la siguiente pregunta de investigación:

[
\text{¿En qué medida un modelo entrenado sobre datos simulados puede capturar patrones operativos transferibles a sistemas reales?}
]

---

## Marco teórico

El análisis se fundamenta en:

* Procesos de Poisson para modelar llegadas de eventos (Ross, 2014)
* Distribuciones binomiales para modelar duplicidad (Casella & Berger, 2002)
* Modelos de cambio de régimen (Hamilton, 1994)
* Ingeniería de confiabilidad en sistemas distribuidos (Beyer et al., 2016)
* Técnicas de deduplicación de contenido (Meyer & Bolosky, 2011)

---

## Alcance y limitaciones

El entorno simulado no pretende reproducir exactamente un sistema real, sino capturar sus propiedades estructurales más relevantes. En consecuencia:

* los resultados obtenidos en simulación deben interpretarse como aproximaciones
* los modelos derivados requieren validación posterior con datos reales
* la utilidad del enfoque depende del grado de alineación entre simulación y sistema objetivo

---

## Contribución esperada

Este trabajo busca aportar:

* Un marco formal para modelar la generación de artefactos en sistemas distribuidos
* Un entorno de simulación reproducible para experimentación controlada
* Un dataset estructurado para análisis estadístico y entrenamiento de modelos
* Evidencia empírica sobre la utilidad del entrenamiento simulado

En última instancia, el objetivo es proporcionar una base cuantitativa que permita mejorar la toma de decisiones en entornos cloud, donde el costo y la eficiencia operativa están directamente ligados al comportamiento del almacenamiento.
