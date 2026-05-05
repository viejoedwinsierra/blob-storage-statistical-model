# 11. Comparativo entre modelamiento y evaluación con datos nuevos

## Propósito

Este documento compara los resultados obtenidos durante la fase de modelamiento estadístico y la fase de evaluación posterior sobre datos nuevos generados por simulación Monte Carlo independiente.

La fase de modelamiento mide el ajuste inicial de los modelos usando partición train/test del dataset base. La fase de evaluación mide la capacidad de generalización usando modelos persistidos, sin reentrenamiento, sobre un dataset nuevo.

## Reportes relacionados

- [Reporte descriptivo](../report/report_descriptive.html)
- [Reporte avanzado multivariado](../report/report_advanced.html)
- [Reporte premodeling](../report/report_premodeling.html)
- [Reporte de modelamiento](../report/report_modeling.html)
- [Reporte de evaluación](../report/report_model_evaluation.html)

## Comparación resumida

| Target | Modelo | Métrica principal en modelamiento | Métrica principal en evaluación | Lectura |
|---|---|---:|---:|---|
| transfer_duration_sec | OLS log-linear | R2 = 0.9579 | R2 = 0.9581 | Modelo altamente estable |
| transfer_duration_sec | GLM Gamma log | R2 = 0.4951 | R2 = 0.5036 | Modelo débil para duración |
| storage_cost | OLS log-linear | R2 = 0.6576 | R2 = 0.6572 | Estable, pero con capacidad moderada |
| storage_cost | GLM Gamma log | R2 = 0.9248 | R2 = 0.9246 | Mejor modelo para costo |
| has_error | Logistic regression | ROC AUC = 0.8156 | ROC AUC = 0.8130 | Estable, pero con recall bajo |

## Interpretación

Los resultados evidencian que los modelos principales mantienen estabilidad cuando son evaluados sobre datos nuevos. En particular, el modelo OLS log-lineal para duración de transferencia mantiene un R2 cercano a 0.96, lo que confirma alta capacidad de generalización. Para costo de almacenamiento, el GLM Gamma con enlace log mantiene un R2 cercano a 0.92, consolidándose como el modelo más apropiado para esta variable positiva y asimétrica.

El GLM Gamma aplicado a duración de transferencia presenta bajo desempeño tanto en modelamiento como en evaluación, por lo que no se recomienda como modelo final para dicho target. Para la clasificación de errores, la regresión logística mantiene un ROC AUC cercano a 0.81, indicando buena discriminación global. Sin embargo, el recall cercano a 0.23 muestra que el umbral de 0.5 es conservador y detecta una proporción reducida de eventos positivos.

## Conclusión de la prueba

La comparación entre modelamiento y evaluación confirma que el pipeline no depende únicamente del ajuste sobre el dataset original. La evaluación con datos nuevos permite validar estabilidad, detectar modelos débiles y justificar la selección final de modelos.

Como resultado, se recomienda:

1. Usar OLS log-linear para `transfer_duration_sec`.
2. Usar GLM Gamma log para `storage_cost`.
3. Mantener logistic regression para `has_error`, pero revisar el umbral de clasificación si el objetivo operativo es maximizar detección de errores.
4. Descartar o dejar como referencia secundaria el GLM Gamma para duración.

## Relación con el trabajo escrito

Este comparativo debe conectarse con:

- La sección de metodología, porque demuestra separación entre entrenamiento y evaluación.
- La sección de resultados, porque resume los hallazgos cuantitativos.
- La sección de conclusiones, porque soporta la selección final de modelos.
- La sección de limitaciones, porque evidencia el bajo recall de clasificación y la necesidad de ajuste posterior.
