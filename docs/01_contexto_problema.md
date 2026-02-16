# 1. Contexto del problema y propósito del análisis

El sistema genera archivos de aproximadamente 1MB por transacción. En condiciones normales,
el crecimiento del almacenamiento es proporcional al volumen transaccional.

Durante eventos de falla (timeouts y reintentos), el volumen puede multiplicarse (factor k),
generando duplicados por contenido (mismo hash) pero con nombres distintos.

El propósito es modelar estadísticamente este fenómeno y evaluar su impacto.
