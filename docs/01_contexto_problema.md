---
🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---

# 1. Contexto del problema y propósito del análisis

En arquitecturas distribuidas basadas en microservicios, los sistemas de almacenamiento de objetos (Object Storage) actúan como repositorios persistentes de artefactos generados por procesos transaccionales. Bajo condiciones normales de operación, el volumen de almacenamiento crece de manera aproximadamente proporcional al número de transacciones ejecutadas en el sistema.

En el escenario analizado, se asume que cada transacción genera un archivo con tamaño promedio cercano a 1 MB, lo cual permite modelar el crecimiento del almacenamiento como un proceso acumulativo en el tiempo.

Sin embargo, en presencia de fallas operativas —tales como *timeouts*, reintentos automáticos, errores de idempotencia o inconsistencias en pipelines de procesamiento— el sistema puede entrar en estados de degradación donde el volumen de archivos generados se incrementa significativamente. Este fenómeno puede modelarse como un factor de amplificación \( k \), que incrementa la tasa efectiva de generación de archivos.

Adicionalmente, estos eventos inducen la aparición de duplicados lógicos: archivos con contenido idéntico (mismo hash o firma digital) pero identificadores distintos (nombres únicos), lo que impide su deduplicación trivial y genera crecimiento innecesario del almacenamiento.

Desde una perspectiva probabilística, este comportamiento puede interpretarse como un proceso estocástico con cambio de régimen, donde coexisten al menos dos estados:

- **Régimen normal**: baja tasa de duplicidad y crecimiento controlado
- **Régimen de incidente**: alta tasa de duplicidad y crecimiento acelerado

El propósito de este análisis es:

- Modelar estadísticamente la generación de archivos y duplicados
- Cuantificar el impacto de la duplicidad en el consumo de almacenamiento
- Evaluar escenarios de crecimiento bajo distintos niveles de carga y falla
- Formular hipótesis contrastables sobre la dinámica del sistema
- Establecer bases para estrategias de control, como deduplicación y políticas de limpieza

Este enfoque se fundamenta en principios de:

- Procesos de Poisson para modelar llegadas de eventos
- Distribuciones binomiales para modelar duplicidad
- Teoría de sistemas dinámicos discretos
- Análisis de sistemas bajo incertidumbre
- Modelos de cambio de régimen (regime-switching)

El análisis busca no solo describir el comportamiento del sistema, sino también proporcionar un marco cuantitativo para la toma de decisiones en entornos cloud, particularmente en la optimización de costos, resiliencia operativa y diseño de arquitecturas escalables.
