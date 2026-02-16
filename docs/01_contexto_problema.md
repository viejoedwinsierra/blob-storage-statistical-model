---
🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---


# 1. Contexto del problema y propósito del análisis

En sistemas distribuidos basados en microservicios, los repositorios de objetos almacenan artefactos transaccionales que crecen proporcionalmente al volumen de operaciones. En el escenario modelado, cada transacción genera un archivo cercano a 1MB.

Durante eventos de falla (timeouts, reintentos automáticos), el volumen puede multiplicarse por un factor k, generando duplicados por contenido (mismo hash) pero con nombres distintos.

El propósito es modelar estadísticamente este fenómeno, evaluar su impacto y formular hipótesis contrastables sobre el comportamiento del sistema.
