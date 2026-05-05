# 11. Comparativo entre modelamiento y evaluación con datos nuevos

## Navegación

- 📊 [Reporte descriptivo](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_descriptive.html)
- 📈 [Reporte avanzado multivariado](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_advanced.html)
- ⚙️ [Reporte premodeling](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_premodeling.html)
- 🧠 [Reporte de modelamiento](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_modeling.html)
- 🧪 [Reporte de evaluación](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_model_evaluation.html)

---

## Propósito

Este documento compara los resultados obtenidos durante la fase de modelamiento estadístico y la fase de evaluación posterior sobre datos nuevos generados por simulación Monte Carlo independiente.

La fase de modelamiento mide el ajuste inicial de los modelos usando partición train/test del dataset base. La fase de evaluación mide la capacidad de generalización usando modelos persistidos, sin reentrenamiento, sobre un dataset nuevo.

---

## Diferencia metodológica entre fases

La fase de modelamiento utiliza partición train/test sobre el mismo dataset, lo cual permite evaluar la capacidad de ajuste del modelo bajo condiciones conocidas.

En contraste, la fase de evaluación utiliza un dataset completamente nuevo generado mediante simulación independiente, sin reentrenamiento de los modelos. Esto permite medir la capacidad de generalización en escenarios no observados durante el entrenamiento.

---

## Comparación resumida

| Target | Modelo | Modelamiento | Evaluación | Estabilidad | Lectura |
|---|---|---|---|---|---|
| transfer_duration_sec | OLS log-linear | R2 = 0.9579 | R2 = 0.9581 | Alta | Modelo altamente estable |
| transfer_duration_sec | GLM Gamma log | R2 = 0.4951 | R2 = 0.5036 | Baja | Modelo débil |
| storage_cost | OLS log-linear | R2 = 0.6576 | R2 = 0.6572 | Media | Estable moderado |
| storage_cost | GLM Gamma log | R2 = 0.9248 | R2 = 0.9246 | Alta | Mejor modelo |
| has_error | Logistic regression | ROC AUC = 0.8156 | ROC AUC = 0.8130 | Alta | Buen discriminador |

---

## Análisis de estabilidad

La comparación entre métricas de modelamiento y evaluación muestra que no existe degradación significativa en el desempeño de los modelos. Las diferencias observadas son marginales, lo que indica ausencia de sobreajuste y adecuada capacidad de generalización.

Este comportamiento es consistente con la estabilidad de la simulación Monte Carlo, la cual genera distribuciones similares entre datasets.

---

## Interpretación

Los resultados evidencian que los modelos principales mantienen estabilidad cuando son evaluados sobre datos nuevos.

- El modelo **OLS log-lineal para duración de transferencia** mantiene un R2 cercano a 0.96, confirmando alta capacidad de generalización.
- Para **costo de almacenamiento**, el **GLM Gamma con enlace log** mantiene un R2 cercano a 0.92, consolidándose como el modelo más apropiado.
- El **GLM Gamma para duración** presenta bajo desempeño en ambas fases, por lo que no se recomienda como modelo final.
- En clasificación, la **regresión logística** mantiene un ROC AUC cercano a 0.81, indicando buena discriminación.

Sin embargo, el **recall bajo (~0.23)** indica que el modelo es conservador en la detección de errores.

Desde una perspectiva operativa, esto implica que:

- El modelo evita falsos positivos
- Pero puede perder eventos reales de error

Esto puede ser adecuado en escenarios donde los falsos positivos son costosos, pero requiere ajuste si la detección de errores es crítica.

---

## Conclusión de la prueba

La comparación entre modelamiento y evaluación confirma que el pipeline no depende únicamente del ajuste sobre el dataset original.

La evaluación con datos nuevos permite:

- validar estabilidad
- detectar modelos débiles
- justificar selección final

---

## Conclusión general del análisis comparativo

La consistencia entre los resultados de modelamiento y evaluación valida tanto la estructura del pipeline como la coherencia de la simulación.

Esto permite concluir que los modelos seleccionados:

- explican adecuadamente los datos de entrenamiento  
- mantienen su desempeño en escenarios nuevos  
- son aplicables en contextos reales  

---

## Recomendaciones

1. Usar **OLS log-linear** para `transfer_duration_sec`.
2. Usar **GLM Gamma log** para `storage_cost`.
3. Mantener **logistic regression** para `has_error`, pero ajustar umbral si se requiere mayor recall.
4. Descartar o dejar como referencia secundaria el GLM Gamma para duración.

---

## Relación con el trabajo escrito

Este comparativo constituye el puente entre la fase de modelamiento y la validación final del trabajo.

Se conecta con:

- **Metodología** → separación entrenamiento vs evaluación  
- **Resultados** → síntesis cuantitativa  
- **Conclusiones** → selección final de modelos  
- **Limitaciones** → bajo recall y ajuste necesario  

Este análisis permite justificar los modelos no solo por su ajuste, sino por su comportamiento en escenarios independientes.
