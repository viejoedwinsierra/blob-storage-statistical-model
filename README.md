# Simulación Monte Carlo y Modelamiento Estadístico para Blob Storage

Este repositorio documenta el trabajo de simulación, análisis exploratorio, premodelamiento, modelamiento estadístico y evaluación de modelos sobre escenarios sintéticos de operación de Blob Storage.

El objetivo principal es construir un pipeline reproducible que permita analizar duración de transferencia, costo de almacenamiento y ocurrencia de errores, usando modelos estadísticos interpretables y evaluación posterior sobre datos nuevos.

---

## Navegación principal

### Documentación metodológica

1. [Contexto del problema](docs/01_contexto_problema.md)
2. [Procedencia y fuente de los datos](docs/02_00_procedencia_fuente.md)
3. [Marco teórico de Blob Storage](docs/02_01_rmarco_teorico_blobstorage.md)
4. [Estructura del dataset](docs/03_00_estructura_dataset.md)
5. [Variables independientes](docs/03_01_variables_independientes.md)
6. [Variables dependientes](docs/03_02_variables_dependientes.md)
7. [Parámetros de simulación](docs/04_parametros_simulacion.md)
8. [Modelamiento estadístico](docs/05_modelamiento_estadistico.md)
9. [Preguntas analíticas](docs/06_preguntas_analiticas.md)
10. [Limitaciones metodológicas](docs/07_limitaciones_metodologia.md)
11. [Validaciones de calidad](docs/08_validaciones_calidad.md)
12. [Modelo causal de fallas](docs/09_modelo_causal_fallas.md)
13. [Referencias](docs/10_referencias.md)
14. [Comparativo modelamiento vs evaluación](docs/11_comparativo_modelamiento_evaluacion.md)

### Reportes HTML generados

- [Reporte descriptivo univariado](report/report_descriptive.html)
- [Reporte avanzado multivariado](report/report_advanced.html)
- [Reporte de premodelamiento](report/report_premodeling.html)
- [Reporte de modelamiento estadístico](report/report_modeling.html)
- [Reporte de evaluación con datos nuevos](report/report_model_evaluation.html)

### Código actualizado sugerido

Los archivos ajustados durante la evolución del pipeline se encuentran en:

- [`src_updates/modeling/`](src_updates/modeling/)
- [`src_updates/model_evaluation/`](src_updates/model_evaluation/)
- [`src_updates/run_premodeling_modeling.py`](src_updates/run_premodeling_modeling.py)
- [`src_updates/run_evaluate_saved_models.py`](src_updates/run_evaluate_saved_models.py)

Estos archivos están listos para copiarse dentro de `src/` respetando la estructura original del proyecto.

---

## Estructura del paquete

```text
.
├── README.md
├── .gitignore
├── docs/
│   ├── 01_contexto_problema.md
│   ├── 02_00_procedencia_fuente.md
│   ├── 02_01_rmarco_teorico_blobstorage.md
│   ├── 03_00_estructura_dataset.md
│   ├── 03_01_variables_independientes.md
│   ├── 03_02_variables_dependientes.md
│   ├── 04_parametros_simulacion.md
│   ├── 05_modelamiento_estadistico.md
│   ├── 06_preguntas_analiticas.md
│   ├── 07_limitaciones_metodologia.md
│   ├── 08_validaciones_calidad.md
│   ├── 09_modelo_causal_fallas.md
│   ├── 10_referencias.md
│   └── 11_comparativo_modelamiento_evaluacion.md
├── report/
│   ├── report_descriptive.html
│   ├── report_advanced.html
│   ├── report_premodeling.html
│   ├── report_modeling.html
│   └── report_model_evaluation.html
├── img/
├── datasets/
└── src_updates/
```

---

## Relación entre informes

El trabajo se organiza como una cadena de evidencia:

1. **Reporte descriptivo**: caracteriza el dataset, la calidad de datos, variables numéricas, categóricas y outliers.
2. **Reporte avanzado multivariado**: analiza relaciones entre variables, correlaciones, redundancias, fuga de información y transformaciones sugeridas.
3. **Reporte premodeling**: documenta targets, variables candidatas, encoding y transformaciones previas al entrenamiento.
4. **Reporte modeling**: entrena modelos estadísticos interpretables y presenta métricas internas.
5. **Reporte model evaluation**: carga modelos persistidos y evalúa sobre datos nuevos sin reentrenar.

Esta relación permite sostener que la simulación no se queda únicamente en generación de datos, sino que alimenta un proceso completo de análisis y validación estadística.

---

## Síntesis metodológica

El pipeline implementado sigue la siguiente lógica:

```text
Simulación Monte Carlo
        ↓
Preparación y limpieza
        ↓
Análisis descriptivo
        ↓
Análisis multivariado exploratorio
        ↓
Premodeling
        ↓
Modelamiento estadístico
        ↓
Persistencia de modelos
        ↓
Evaluación sobre datos nuevos
```

La fase de evaluación se considera crítica porque separa el ajuste inicial del modelo de su capacidad de generalización. Los modelos guardados se aplican sobre un dataset nuevo generado con la misma configuración experimental, sin reentrenamiento.

---

## Resultados principales

### Duración de transferencia

El modelo OLS log-lineal presenta el mejor desempeño para `transfer_duration_sec`, con R2 cercano a 0.96 tanto en modelamiento como en evaluación. Esto indica alta estabilidad y adecuada generalización.

El GLM Gamma log aplicado a duración presenta R2 cercano a 0.50, por lo que se considera un modelo débil para este target.

### Costo de almacenamiento

El GLM Gamma log presenta el mejor desempeño para `storage_cost`, con R2 cercano a 0.92 en entrenamiento y evaluación. Esto es coherente con la naturaleza positiva y asimétrica del costo.

El OLS log-lineal para costo es estable, pero con capacidad explicativa moderada.

### Ocurrencia de errores

La regresión logística mantiene ROC AUC cercano a 0.81, lo que indica buena capacidad discriminante. Sin embargo, el recall es bajo, por lo que se recomienda revisar el umbral de clasificación si el objetivo operativo es detectar más eventos positivos.

---

## Conclusiones de la prueba

1. La simulación Monte Carlo permitió construir un dataset controlado y suficientemente rico para análisis estadístico.
2. La fase descriptiva y multivariada permitió identificar variables relevantes, colas pesadas, outliers y posibles fugas de información.
3. El premodeling dejó trazabilidad sobre transformaciones, encoding y selección de variables por target.
4. Los modelos estadísticos interpretables fueron suficientes para capturar patrones relevantes sin recurrir a machine learning complejo.
5. La evaluación sobre datos nuevos confirmó estabilidad en los modelos principales y permitió detectar modelos débiles.
6. La persistencia de modelos evita reentrenamiento innecesario y permite evaluación posterior reproducible.

---

## Recomendaciones finales

- Usar OLS log-linear como modelo principal para duración de transferencia.
- Usar GLM Gamma log como modelo principal para costo de almacenamiento.
- Mantener regresión logística para ocurrencia de errores, pero revisar threshold.
- No usar el GLM Gamma log para duración como modelo final.
- Mantener los modelos `.pkl` fuera del repositorio Git por tamaño y porque son artefactos regenerables.

---

## Notas de reproducibilidad

Los archivos `.pkl`, datasets grandes y salidas generadas deben permanecer fuera de Git. Para ello se incluye un `.gitignore` que excluye artefactos pesados y regenerables.

Para regenerar modelos en el proyecto original:

```bash
python src/run_premodeling_modeling_.py
```

Para evaluar modelos guardados sobre datos nuevos:

```bash
python src/run_evaluate_saved_models.py
```

---

## Referencias bibliográficas base

- James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). *An Introduction to Statistical Learning*.
- McCullagh, P., & Nelder, J. A. (1989). *Generalized Linear Models*.
- Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning*.
- Microsoft. Azure Blob Storage Documentation.
- Rubinstein, R. Y., & Kroese, D. P. (2016). *Simulation and the Monte Carlo Method*.
