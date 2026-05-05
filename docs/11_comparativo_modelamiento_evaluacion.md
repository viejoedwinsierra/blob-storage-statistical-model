# 11. Comparativo entre modelamiento y evaluación con datos nuevos

## Navegación

### Reportes publicados

- 📊 [Reporte descriptivo](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_descriptive.html)
- 📈 [Reporte avanzado multivariado](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_advanced.html)
- ⚙️ [Reporte premodeling](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_premodeling.html)
- 🧠 [Reporte de modelamiento](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_modeling.html)
- 🧪 [Reporte de evaluación](https://viejoedwinsierra.github.io/blob-storage-statistical-model/report/report_model_evaluation.html)

### Fuente para descarga

- 📊 [Fuente reporte descriptivo](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_descriptive.html)
- 📈 [Fuente reporte avanzado](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_advanced.html)
- ⚙️ [Fuente reporte premodeling](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_premodeling.html)
- 🧠 [Fuente reporte modelamiento](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_modeling.html)
- 🧪 [Fuente reporte evaluación](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_model_evaluation.html)

---

## Propósito

Este documento compara los resultados obtenidos durante la fase de modelamiento estadístico y la fase de evaluación posterior sobre datos nuevos generados por simulación Monte Carlo independiente.

La fase de modelamiento mide el ajuste inicial de los modelos usando partición train/test del dataset base.

La fase de evaluación mide la capacidad de generalización usando modelos persistidos, sin reentrenamiento, sobre un dataset nuevo.

El objetivo no es solo comparar métricas, sino explicar si el pipeline completo:

- genera datos consistentes;
- permite construir modelos estables;
- evita sobreajuste evidente;
- identifica modelos fuertes y débiles;
- produce resultados interpretables para toma de decisiones.

---

## Salida general del proceso

El proceso completo puede entenderse como una cadena de evidencia:

```text
Simulación Monte Carlo
        ↓
Reporte descriptivo
        ↓
Reporte avanzado multivariado
        ↓
Reporte premodeling
        ↓
Reporte de modelamiento
        ↓
Reporte de evaluación
        ↓
Comparativo final

Cada reporte cumple una función dentro del pipeline:

Fase	Reporte	Salida principal
Descriptiva	report_descriptive.html	Distribuciones, outliers, colas pesadas y calidad de datos
Multivariada	report_advanced.html	Relaciones entre variables, redundancia y fuga de información
Premodeling	report_premodeling.html	Variables finales, transformaciones y datasets por target
Modelamiento	report_modeling.html	Modelos entrenados y métricas internas
Evaluación	report_model_evaluation.html	Generalización sobre dataset nuevo sin reentrenamiento
Evidencia descriptiva del proceso

El reporte descriptivo mostró que el dataset contiene variables con colas pesadas y necesidad de transformación, especialmente:

size_mb
transfer_duration_sec
transfer_speed_mbps
storage_cost

Esto justifica que el modelamiento no se realice directamente sobre escala original en todos los casos.

Lectura

La presencia de asimetría positiva, outliers y colas largas indica que el sistema simulado reproduce una característica típica de almacenamiento cloud: muchos objetos pequeños y pocos objetos grandes con impacto operativo y económico mayor.

Por esta razón, las transformaciones logarítmicas no son un detalle técnico menor, sino una decisión metodológica necesaria.

Evidencia multivariada previa al modelamiento

El análisis avanzado permitió identificar relaciones estructurales entre variables.

Entre los hallazgos más importantes se destacan:

Relación	Correlación Spearman	Lectura
size_mb → storage_cost	0.9098	Relación fuerte
size_mb → transfer_duration_sec	0.7800	Relación fuerte
congestion_factor → queue_pressure	0.9451	Redundancia operativa
hourly_arrival_count → queue_pressure	0.8993	Relación derivada

Lectura

El tamaño del archivo aparece como variable estructural para explicar costo y duración.
Por otro lado, variables como congestion_factor y queue_pressure muestran alta relación entre sí, por lo que deben tratarse con cuidado para evitar redundancia en el modelamiento.

Diferencia metodológica entre fases

La fase de modelamiento utiliza partición train/test sobre el mismo dataset, lo cual permite evaluar la capacidad de ajuste del modelo bajo condiciones conocidas.

En contraste, la fase de evaluación utiliza un dataset completamente nuevo generado mediante simulación independiente, sin reentrenamiento de los modelos.

Modelamiento:
Dataset base → train/test → entrenamiento → prueba interna

Evaluación:
Dataset nuevo → modelos guardados → predicción sin reentrenamiento

Esta diferencia es clave porque la evaluación con datos nuevos permite analizar generalización y estabilidad.

Comparación resumida
Target	Modelo	Modelamiento	Evaluación	Estabilidad	Lectura
transfer_duration_sec	OLS log-linear	R² = 0.9579	R² = 0.9581 aprox.	Alta	Modelo altamente estable
transfer_duration_sec	GLM Gamma log	R² = 0.4951	R² ≈ 0.50	Baja	Modelo débil para duración
storage_cost	OLS log-linear	R² = 0.6576	R² ≈ 0.656	Media	Estable, pero moderado
storage_cost	GLM Gamma log	R² = 0.9248	R² ≈ 0.925	Alta	Mejor modelo para costo
has_error	Logistic regression	ROC AUC = 0.8156	ROC AUC ≈ 0.815	Alta	Buen discriminador, bajo recall
Evidencia de modelamiento
Duración de transferencia

Lectura

El modelo OLS log-linear muestra alta alineación entre valores observados y predichos.
Esto confirma que la duración de transferencia sigue una estructura aproximadamente multiplicativa, donde el tamaño, la velocidad, la congestión y variables temporales explican gran parte de la variabilidad.

Costo de almacenamiento

Lectura

El GLM Gamma con enlace log es el modelo más coherente para costo porque storage_cost es una variable:

positiva;
continua;
asimétrica;
con varianza creciente.

El modelo captura mejor esta estructura que un OLS simple.

Clasificación de errores

Lectura

La regresión logística presenta buena capacidad discriminativa global, pero la matriz de confusión y la calibración muestran que no basta con mirar el ROC AUC.

El modelo puede ordenar casos por riesgo, pero no necesariamente detectar todos los errores relevantes con el umbral por defecto.

Evidencia de evaluación con datos nuevos
Resumen de métricas

Lectura

Los deltas de métricas muestran que no hay degradación significativa entre modelamiento y evaluación.
Esto sugiere que los modelos no dependen únicamente del dataset inicial, sino que capturan patrones estables del proceso simulado.

Evaluación de duración

Lectura

El modelo de duración mantiene estabilidad en datos nuevos.
Esto valida el uso del OLS log-linear como modelo principal para transfer_duration_sec.

Evaluación de costo

Lectura

El GLM Gamma mantiene alto desempeño sobre el dataset de evaluación.
Esto confirma que el costo fue modelado de forma adecuada bajo la estructura probabilística del simulador.

Evaluación de clasificación

Lectura

La regresión logística mantiene un ROC AUC estable, pero el recall bajo evidencia una limitación operativa importante.

Esto implica que el modelo:

discrimina razonablemente bien;
conserva estabilidad;
pero detecta pocos positivos con el umbral actual.

En escenarios donde los errores tienen alto impacto, se recomienda ajustar el threshold.

Análisis por proceso
1. Proceso descriptivo

El proceso descriptivo permitió identificar:

variables con asimetría;
necesidad de transformación logarítmica;
outliers relevantes;
variables de alta cardinalidad;
eventos de error desbalanceados.

Este proceso justificó decisiones posteriores de modelamiento.

2. Proceso multivariado

El análisis multivariado permitió identificar:

relaciones fuertes entre tamaño, costo y duración;
redundancia entre variables operativas;
posibles riesgos de fuga de información;
variables derivadas que requieren control.

Este proceso evitó usar variables sin justificación y permitió preparar mejor el premodeling.

3. Proceso de premodeling

El premodeling definió datasets específicos por target:

duración: regresión positiva;
costo: regresión positiva;
error: clasificación binaria.

También definió transformaciones como log1p y codificación de variables categóricas.

La principal contribución de esta fase fue convertir el dataset bruto en estructuras listas para modelamiento.

4. Proceso de modelamiento

El modelamiento confirmó que:

OLS log-linear es altamente estable para duración;
GLM Gamma es superior para costo;
regresión logística es útil para error, pero imperfecta.

Esta fase permitió seleccionar modelos interpretables y defendibles.

5. Proceso de evaluación

La evaluación permitió validar modelos guardados sobre datos nuevos.

Esto es metodológicamente más fuerte que limitarse a train/test porque simula una condición más cercana a uso real:

modelo entrenado → datos nuevos → desempeño observado

La estabilidad observada confirma que la simulación genera datasets consistentes.

Modelos seleccionados
Target	Modelo recomendado	Justificación
transfer_duration_sec	OLS log-linear	Mayor estabilidad y mejor R²
storage_cost	GLM Gamma log	Mejor ajuste para variable positiva y asimétrica
has_error	Logistic regression	Buen ROC AUC, pero requiere ajuste de threshold
Modelos descartados o secundarios
Modelo	Motivo
GLM Gamma para duración	Bajo R² frente a OLS log-linear
OLS log-linear para costo	Estable, pero menor capacidad explicativa que GLM Gamma
Logistic regression sin ajuste de threshold	Bajo recall para detección de errores
Conclusión general del análisis comparativo

La comparación entre modelamiento y evaluación confirma que el pipeline no depende únicamente del ajuste sobre el dataset original.

La evaluación con datos nuevos permite concluir que:

los modelos principales son estables;
no hay evidencia fuerte de sobreajuste;
la simulación genera datos consistentes;
el GLM Gamma es el mejor modelo para costo;
el OLS log-linear es el mejor modelo para duración;
la clasificación requiere ajuste operativo.

En síntesis:

El pipeline demuestra coherencia entre simulación, análisis descriptivo, selección de variables, modelamiento y evaluación externa.

Recomendaciones finales
Usar OLS log-linear como modelo principal para duración.
Usar GLM Gamma log como modelo principal para costo.
Mantener regresión logística para errores, pero ajustar threshold.
Monitorear recall y Precision-Recall, no solo ROC AUC.
Mantener la evaluación con dataset nuevo como práctica estándar.
Usar los reportes HTML como evidencia navegable y los archivos fuente como soporte descargable.

---

# 11.X Conclusiones generales del proyecto

## 1. Validez del enfoque metodológico

El proyecto demuestra que es posible construir un pipeline completo de análisis y modelamiento estadístico basado en datos simulados, siempre que:

* el proceso generativo esté bien definido;
* las distribuciones sean coherentes con el fenómeno modelado;
* exista alineación entre simulación y modelamiento.

La consistencia observada entre modelamiento y evaluación confirma que el enfoque no es arbitrario, sino estructuralmente válido.

---

## 2. Sobre la capacidad de generalización

La evaluación sobre un dataset nuevo, generado mediante simulación independiente, muestra que:

* no existe degradación significativa en métricas;
* los modelos mantienen su comportamiento;
* las relaciones entre variables se preservan.

Esto permite afirmar que:

> Los modelos capturan patrones del sistema simulado, no ruido específico del dataset inicial.

---

## 3. Sobre los modelos estadísticos utilizados

### Duración de transferencia

El modelo OLS log-linear presenta:

* alto poder explicativo;
* estabilidad entre datasets;
* coherencia con la estructura multiplicativa del fenómeno.

Se valida como modelo principal para este target.

---

### Costo de almacenamiento

El modelo GLM Gamma con enlace log resulta el más adecuado debido a que:

* el costo es una variable positiva;
* presenta asimetría;
* su varianza crece con la media.

El modelo captura correctamente esta estructura y mantiene estabilidad en evaluación.

---

### Clasificación de errores

La regresión logística presenta:

* buen desempeño global (ROC AUC);
* estabilidad entre datasets;
* limitaciones en recall.

Esto implica que:

> El modelo es útil para priorización de riesgo, pero no suficiente para detección exhaustiva de errores sin ajustes adicionales.

---

## 4. Sobre el rol del análisis descriptivo y multivariado

El análisis descriptivo y multivariado no fue una etapa exploratoria superficial, sino un componente clave para:

* identificar transformaciones necesarias;
* detectar relaciones estructurales;
* evitar variables redundantes;
* justificar decisiones de modelamiento.

En particular, la identificación de colas pesadas y relaciones fuertes con `size_mb` fue determinante para el éxito del modelamiento.

---

## 5. Sobre el valor del simulador

El simulador no solo permitió generar datos, sino:

* construir escenarios controlados;
* validar hipótesis;
* comparar modelos en condiciones reproducibles;
* separar claramente entrenamiento y evaluación.

Esto convierte el proyecto en un entorno experimental, no solo analítico.

---

## 6. Sobre las limitaciones del enfoque

A pesar de los resultados positivos, el proyecto presenta limitaciones claras:

* los datos son sintéticos;
* el sistema no incluye todos los factores reales de infraestructura;
* el modelo de costo es simplificado;
* la clasificación de errores presenta bajo recall;
* no se modelan dependencias temporales complejas.

Por lo tanto:

> Los resultados son válidos dentro del entorno simulado, pero requieren calibración para producción real.

---

## 7. Implicaciones prácticas

El pipeline desarrollado permite:

* estimar costos antes de su ejecución;
* analizar eficiencia del almacenamiento;
* identificar condiciones de riesgo;
* diseñar alertas tempranas;
* simular escenarios operativos.

Esto lo hace relevante para:

* optimización de costos cloud;
* análisis de sistemas distribuidos;
* monitoreo operativo.

---

## 8. Qué se demuestra realmente

Este trabajo demuestra que:

* un sistema complejo puede modelarse mediante variables a nivel de archivo;
* los modelos estadísticos clásicos siguen siendo altamente efectivos cuando el problema está bien definido;
* la simulación controlada es una herramienta válida para análisis y validación;
* la separación entre modelamiento y evaluación es clave para evitar conclusiones engañosas.

---

## 9. Qué no se puede afirmar

No se puede afirmar que:

* los modelos predicen con exactitud un sistema real sin ajustes;
* el costo modelado equivale a facturación real completa;
* el modelo de clasificación detecta todos los errores;
* el comportamiento observado se mantendrá en producción sin recalibración.

---

## 10. Cierre del proyecto

El pipeline completo:

```text
Simulación → Análisis → Modelamiento → Evaluación → Comparación
```

demuestra coherencia metodológica, estabilidad estadística y capacidad explicativa.

El valor principal del trabajo no está únicamente en los modelos, sino en la integración completa del proceso, desde la generación de datos hasta la validación final.

---
