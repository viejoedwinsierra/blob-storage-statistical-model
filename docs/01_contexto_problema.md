---
🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---

# 1. Contexto del problema y propósito del análisis

En arquitecturas distribuidas basadas en microservicios, los sistemas de almacenamiento de objetos (Object Storage) actúan como repositorios persistentes de artefactos generados por procesos transaccionales. Este tipo de arquitecturas, ampliamente descritas en (Kleppmann, 2017), se caracterizan por la generación continua de datos derivados de múltiples servicios desacoplados, lo que implica un crecimiento sostenido del volumen almacenado.

Bajo condiciones normales de operación, el volumen de almacenamiento crece de manera aproximadamente proporcional al número de transacciones ejecutadas en el sistema, lo cual es consistente con modelos de procesamiento distribuido como los planteados en el paradigma de (Dean & Ghemawat, 2008), donde el procesamiento de grandes volúmenes de datos se distribuye en múltiples nodos generando artefactos intermedios y finales.

En el escenario analizado, se asume que cada transacción genera un archivo con tamaño promedio cercano a 1 MB, lo cual permite modelar el crecimiento del almacenamiento como un proceso acumulativo en el tiempo. Desde una perspectiva probabilística, la llegada de estos eventos puede aproximarse mediante un proceso de Poisson, ampliamente utilizado para modelar ocurrencias independientes en el tiempo (Ross, 2014).

Sin embargo, en presencia de fallas operativas —tales como *timeouts*, reintentos automáticos, errores de idempotencia o inconsistencias en pipelines de procesamiento— el sistema puede entrar en estados de degradación donde el volumen de archivos generados se incrementa significativamente. Este comportamiento ha sido ampliamente documentado en prácticas de ingeniería de confiabilidad como las descritas en (Beyer et al., 2016), donde los sistemas distribuidos presentan patrones distintos bajo condiciones de fallo.

Este fenómeno puede modelarse como un factor de amplificación \( k \), que incrementa la tasa efectiva de generación de archivos. En particular, los mecanismos de retry sin control adecuado de idempotencia —tal como se discute en (Kleppmann, 2017)— pueden inducir la generación de múltiples artefactos para una misma transacción lógica.

Adicionalmente, estos eventos inducen la aparición de duplicados lógicos: archivos con contenido idéntico (mismo hash o firma digital) pero identificadores distintos (nombres únicos), lo que impide su deduplicación trivial y genera crecimiento innecesario del almacenamiento. Este problema ha sido estudiado en profundidad en sistemas de almacenamiento mediante técnicas de deduplicación de contenido, como en el trabajo de Meyer y Bolosky (2011).

Desde una perspectiva probabilística, este comportamiento puede interpretarse como un proceso estocástico con cambio de régimen, en el cual el sistema alterna entre diferentes estados operativos. Este tipo de modelos, conocidos como modelos de cambio de régimen, han sido ampliamente utilizados en análisis de series de tiempo (Hamilton, 1994) para representar dinámicas con múltiples estados latentes.

En este contexto, se identifican al menos dos regímenes:

- **Régimen normal**: baja tasa de duplicidad y crecimiento controlado  
- **Régimen de incidente**: alta tasa de duplicidad y crecimiento acelerado  

El propósito de este análisis es:

- Modelar estadísticamente la generación de archivos y duplicados  
- Cuantificar el impacto de la duplicidad en el consumo de almacenamiento  
- Evaluar escenarios de crecimiento bajo distintos niveles de carga y falla  
- Formular hipótesis contrastables sobre la dinámica del sistema  
- Establecer bases para estrategias de control, como deduplicación y políticas de limpieza  

Este enfoque se fundamenta en principios de:

- Procesos de Poisson para modelar llegadas de eventos (Ross, 2014)  
- Distribuciones binomiales para modelar duplicidad (Casella & Berger, 2002)  
- Teoría de sistemas dinámicos discretos  
- Análisis de sistemas bajo incertidumbre  
- Modelos de cambio de régimen (Hamilton, 1994)  

El análisis busca no solo describir el comportamiento del sistema, sino también proporcionar un marco cuantitativo para la toma de decisiones en entornos cloud. En particular, plataformas como Microsoft Azure (2023) y Amazon Web Services (2023) estructuran sus modelos de costos en función del volumen almacenado, lo que implica que fenómenos como la duplicidad impactan directamente la eficiencia económica, la resiliencia operativa y el diseño de arquitecturas escalables.
