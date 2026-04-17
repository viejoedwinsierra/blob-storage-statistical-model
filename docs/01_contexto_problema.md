---

🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---

# 1. Contexto del problema y propósito del análisis

En arquitecturas distribuidas basadas en microservicios, los sistemas de almacenamiento de objetos (*Object Storage*) constituyen la capa persistente donde se materializan los artefactos generados por procesos transaccionales y de integración. Estos sistemas —como los ofrecidos por plataformas cloud— operan bajo un modelo de crecimiento continuo impulsado por la ejecución de múltiples servicios desacoplados, lo que introduce dinámicas complejas de generación, replicación y almacenamiento de datos.

Bajo condiciones normales de operación, el volumen de almacenamiento crece de manera aproximadamente proporcional al número de transacciones ejecutadas. Este comportamiento es consistente con modelos de procesamiento distribuido (Dean & Ghemawat, 2008), donde cada operación puede generar artefactos intermedios y finales que se almacenan de forma persistente. En este contexto, es razonable modelar la llegada de eventos como un proceso de Poisson (Ross, 2014), donde las ocurrencias son independientes y distribuidas en el tiempo.

Sin embargo, esta aproximación resulta insuficiente cuando el sistema entra en estados de degradación operativa. En presencia de fallas como *timeouts*, reintentos automáticos, errores de idempotencia o inconsistencias en pipelines, la relación entre transacciones y artefactos almacenados deja de ser lineal. En estos escenarios, el sistema presenta un comportamiento amplificado donde una misma transacción lógica puede generar múltiples artefactos físicos.

Este fenómeno puede formalizarse mediante un factor de amplificación ( k ), tal que la tasa efectiva de generación de archivos se incrementa:

$$
\lambda' = k \cdot \lambda
$$

donde ( \lambda ) representa la tasa de generación en condiciones normales. En particular, la ausencia de mecanismos robustos de idempotencia —como se discute en (Kleppmann, 2017)— conduce a la generación de duplicados lógicos: archivos con contenido idéntico (mismo hash o firma digital) pero identificadores distintos, lo que impide su eliminación mediante estrategias triviales de deduplicación.

Este comportamiento ha sido ampliamente documentado en ingeniería de confiabilidad (Beyer et al., 2016), donde los sistemas distribuidos exhiben patrones significativamente distintos bajo condiciones de fallo. Desde una perspectiva probabilística, esto implica un cambio en el régimen del sistema, el cual puede modelarse mediante procesos estocásticos con cambio de estado (Hamilton, 1994).

En este contexto, el sistema puede conceptualizarse como un proceso con al menos dos regímenes operativos:

* **Régimen normal**: crecimiento controlado, baja duplicidad, comportamiento cercano a Poisson
* **Régimen de incidente**: crecimiento acelerado, alta duplicidad, presencia de correlaciones inducidas por reintentos y fallas

La transición entre estos regímenes no es directamente observable, lo que introduce un componente latente en la dinámica del sistema.

---

## Problema central

El problema fundamental no radica únicamente en el crecimiento del almacenamiento, sino en la **incertidumbre estructural sobre la relación entre eventos lógicos y artefactos físicos generados**. Esta incertidumbre afecta:

* la estimación del volumen real de almacenamiento requerido
* la detección temprana de anomalías operativas
* la eficiencia de los mecanismos de deduplicación
* la capacidad de predecir comportamientos bajo carga o falla

Adicionalmente, dado que los modelos de costo en plataformas cloud dependen directamente del volumen almacenado, este fenómeno impacta de forma directa la eficiencia económica del sistema.

---

## Enfoque del análisis

Debido a la dificultad de acceder a datos reales completos y controlados en entornos productivos, se propone un enfoque basado en **simulación estructurada**. En este enfoque, se construye un dataset sintético que replica:

* la distribución temporal de eventos
* la generación de artefactos documentales
* la inyección controlada de errores operativos
* la aparición de duplicados lógicos
* la variabilidad en rutas y almacenamiento

Este dataset actúa como un entorno experimental controlado que permite analizar el comportamiento del sistema bajo diferentes configuraciones.

---

## Propósito del estudio

El propósito de este análisis es múltiple:

* Modelar estadísticamente la generación de archivos en sistemas distribuidos
* Caracterizar la dinámica de duplicidad bajo diferentes regímenes operativos
* Evaluar el impacto de la amplificación en el consumo de almacenamiento
* Diseñar y validar modelos estadísticos y de *machine learning* para la detección de errores
* Analizar la transferibilidad del conocimiento aprendido en simulación hacia escenarios reales

En particular, se busca responder a la siguiente pregunta de investigación:

[
\text{¿En qué medida un modelo entrenado sobre datos simulados puede capturar patrones operativos transferibles a sistemas reales?}
]

---

## Marco teórico

El análisis se fundamenta en:

* Procesos de Poisson para modelar llegadas de eventos (Ross, 2014)
* Distribuciones binomiales para modelar eventos de duplicidad (Casella & Berger, 2002)
* Modelos de cambio de régimen para representar estados latentes (Hamilton, 1994)
* Principios de ingeniería de confiabilidad en sistemas distribuidos (Beyer et al., 2016)
* Técnicas de deduplicación de contenido en almacenamiento (Meyer & Bolosky, 2011)

---

## Alcance y limitaciones

Es importante reconocer que el entorno simulado no pretende reproducir exactamente un sistema real, sino capturar sus propiedades estructurales más relevantes. En consecuencia:

* los resultados obtenidos en simulación deben interpretarse como aproximaciones
* los modelos derivados requieren validación posterior con datos reales
* la utilidad del enfoque depende del grado de alineación entre simulación y sistema objetivo

---

## Contribución esperada

Este trabajo busca aportar:

* Un marco formal para modelar la generación de artefactos en sistemas distribuidos
* Un entorno de simulación reproducible para experimentación controlada
* Un dataset estructurado para análisis estadístico y entrenamiento de modelos
* Evidencia empírica sobre la utilidad del entrenamiento simulado en problemas reales

En última instancia, el objetivo es proporcionar una base cuantitativa que permita mejorar la toma de decisiones en arquitecturas cloud, particularmente en plataformas como Microsoft Azure y Amazon Web Services, donde el costo y la eficiencia operativa están directamente ligados al comportamiento del almacenamiento.
