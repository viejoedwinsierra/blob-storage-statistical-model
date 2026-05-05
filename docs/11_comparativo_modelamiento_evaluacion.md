# 11. Comparativo entre modelamiento y evaluación con datos nuevos

---

## 📥 Acceso a reportes (descarga directa)

* 📊 [Reporte descriptivo](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_descriptive.html)
* 📈 [Reporte multivariado](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_advanced.html)
* ⚙️ [Reporte premodeling](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_premodeling.html)
* 🧠 [Reporte modelamiento](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_modeling.html)
* 🧪 [Reporte evaluación](https://github.com/viejoedwinsierra/blob-storage-statistical-model/blob/main/report/report_model_evaluation.html)

---

## 🎯 Propósito

Este documento analiza la diferencia entre:

* el desempeño de los modelos durante el **modelamiento**
* su comportamiento en un **dataset nuevo (evaluación)**

El objetivo es determinar si los modelos:

* generalizan correctamente
* capturan relaciones estructurales
* evitan sobreajuste

---

## 🔄 Flujo del proceso analítico

```text
Simulación → Descriptivo → Multivariado → Premodeling → Modelamiento → Evaluación
```

---

## 📊 1. Evidencia estructural del sistema

### Relaciones principales

![Relaciones costo](../img/advanced/target_relationships_storage_cost.png)

![Relaciones duración](../img/advanced/target_relationships_transfer_duration_sec.png)

---

### 🧠 Interpretación

* `size_mb` es el driver principal del sistema
* costo y duración dependen directamente del tamaño
* existen relaciones estructurales fuertes

👉 Esto explica por qué los modelos funcionan bien

---

## 📈 2. Comparación de desempeño

### Resumen de modelos

| Target   | Modelo         | Modelamiento | Evaluación | Estabilidad |
| -------- | -------------- | ------------ | ---------- | ----------- |
| Duración | OLS log-linear | 0.9579       | ~0.958     | Alta        |
| Duración | GLM Gamma      | 0.495        | ~0.50      | Baja        |
| Costo    | OLS log-linear | 0.657        | ~0.656     | Media       |
| Costo    | GLM Gamma      | 0.924        | ~0.925     | Alta        |
| Error    | Logistic       | 0.815        | ~0.815     | Alta        |

---

## 📊 3. Evidencia de evaluación

### Estabilidad de métricas

![Metric deltas](../img/model_evaluation/evaluation_metric_deltas.png)

---

### 🧠 Interpretación

* Cambios ≈ 0
* No hay degradación

👉 Esto indica:

> Los modelos generalizan dentro del entorno simulado

---

## 📊 4. Evaluación por tipo de problema

---

### ⏱️ Duración (mejor modelo)

![Duración evaluación](../img/model_evaluation/evaluation_log_observed_vs_predicted_transfer_duration_sec_ols_log_linear.png)

**Resultado:**

* Alta precisión
* Alta estabilidad

---

### 💰 Costo (mejor modelo)

![Costo evaluación](../img/model_evaluation/evaluation_log_observed_vs_predicted_storage_cost_glm_gamma_log.png)

**Resultado:**

* Modelo consistente
* Captura asimetría

---

### ⚠️ Error (limitación)

![ROC evaluación](../img/model_evaluation/evaluation_roc_curve_has_error_logistic_regression.png)

![Precision Recall](../img/model_evaluation/evaluation_precision_recall_curve_has_error_logistic_regression.png)

---

### 🧠 Interpretación

* Buen ROC
* Bajo recall

👉 Problema:

> El modelo no detecta suficientes errores

---

## 🔍 5. Análisis por proceso

---

### 🟢 Descriptivo

* Identifica colas pesadas
* Detecta necesidad de transformación

---

### 🟡 Multivariado

* Detecta relaciones clave
* Evita redundancia

---

### 🟡 Premodeling

* Define variables correctas
* Aplica transformaciones

---

### 🟢 Modelamiento

* Selecciona modelos adecuados
* Confirma estructura

---

### 🟢 Evaluación

* Valida generalización
* confirma estabilidad

---

## 🧠 6. Diagnóstico global

### Fortalezas

* Pipeline coherente
* Modelos interpretables
* Alta estabilidad
* Buen diseño de datos

---

### Limitaciones

* Datos simulados
* Bajo recall en clasificación
* Dependencia del simulador

---

## 🏆 7. Modelos finales recomendados

| Target   | Modelo                            |
| -------- | --------------------------------- |
| Duración | OLS log-linear                    |
| Costo    | GLM Gamma                         |
| Error    | Logistic (con threshold ajustado) |

---

# 🧾 Conclusión general

El análisis comparativo demuestra que:

* los modelos capturan relaciones estructurales
* no existe sobreajuste significativo
* la simulación genera datos consistentes
* el pipeline es metodológicamente sólido

---

# 🔥 Insight final

> Un sistema bien modelado estadísticamente puede ser altamente predecible incluso sin modelos complejos.

Pero:

> La simulación no reemplaza la validación con datos reales.

---
