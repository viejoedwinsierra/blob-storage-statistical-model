# 📊 Simulación Monte Carlo y Modelamiento Estadístico para Blob Storage

Este repositorio contiene un proyecto integral de **simulación, análisis estadístico y modelamiento aplicado** a operaciones de Blob Storage, con énfasis en:

* Duración de transferencia
* Costo de almacenamiento
* Ocurrencia de errores

El trabajo está diseñado como un **pipeline reproducible y defendible**, que va desde la generación de datos hasta la evaluación de modelos sobre información completamente nueva.

---

## 🎯 ¿Qué encontrarás en este proyecto?

Este repositorio no es solo código o reportes aislados. Es una **cadena completa de evidencia estadística**, estructurada en cinco etapas:

```text
Simulación → Análisis → Premodeling → Modelamiento → Evaluación
```

Cada etapa está documentada y respaldada por reportes y decisiones metodológicas explícitas.

---

## 🧭 Cómo navegar la documentación

Dependiendo de tu objetivo, puedes recorrer el proyecto de distintas formas:

### 📘 Lectura académica (tipo tesis)

Sigue este orden:

1. Contexto del problema
2. Fuente y estructura de datos
3. Marco teórico
4. Análisis exploratorio
5. Premodeling
6. Modelamiento
7. Evaluación
8. Limitaciones y conclusiones

👉 Empieza aquí:

* [Contexto del problema](docs/01_contexto_problema.md)

---

### 📈 Análisis rápido de resultados

Si quieres entender rápidamente los hallazgos:

* [Reporte de modelamiento](report/report_modeling.html)
* [Evaluación con datos nuevos](report/report_model_evaluation.html)
* [Comparativo modelamiento vs evaluación](docs/11_comparativo_modelamiento_evaluacion.md)

---

### ⚙️ Revisión técnica del pipeline

Para entender implementación:

* [`src_updates/modeling/`](src_updates/modeling/)
* [`src_updates/model_evaluation/`](src_updates/model_evaluation/)
* [`run_premodeling_modeling.py`](src_updates/run_premodeling_modeling.py)
* [`run_evaluate_saved_models.py`](src_updates/run_evaluate_saved_models.py)

---

## 🧱 Estructura del proyecto

```text
.
├── docs/                # Documentación metodológica completa
├── report/              # Reportes HTML generados
├── datasets/            # Datos simulados
├── src_updates/         # Código actualizado del pipeline
├── img/                 # Recursos visuales
└── README.md
```

---

## 🔗 Relación entre los reportes

Los reportes no son independientes. Están diseñados como una secuencia lógica:

1. **Descriptivo**
   → Calidad de datos, distribuciones, outliers

2. **Multivariado**
   → Relaciones, correlaciones, redundancias

3. **Premodeling**
   → Selección de variables, transformaciones

4. **Modelamiento**
   → Entrenamiento y métricas internas

5. **Evaluación**
   → Validación con datos nuevos sin reentrenar

Esta estructura permite pasar de exploración a **validación real de modelos**.

---

## 🧪 Enfoque metodológico

El proyecto se basa en:

* **Simulación Monte Carlo** para generar datos controlados
* **Estadística aplicada** para análisis e inferencia
* **Modelos interpretables** (OLS, GLM, Logística)
* **Evaluación fuera de muestra realista**

Un elemento clave es que los modelos se evalúan en un **dataset completamente nuevo**, lo que aproxima un escenario de producción.

---

## 📊 Resultados clave (visión ejecutiva)

* **Duración**
  → OLS log-linear altamente estable (R² ≈ 0.96)

* **Costo**
  → GLM Gamma es el mejor modelo (R² ≈ 0.92)

* **Errores**
  → Regresión logística con buen ROC AUC (~0.81)
  → Pero bajo recall (limitación importante)

* **Generalización**
  → No hay degradación entre entrenamiento y evaluación

---

## 🧠 Qué hace valioso este proyecto

Este trabajo demuestra que:

* La simulación puede integrarse con modelamiento realista
* Los modelos estadísticos clásicos siguen siendo altamente efectivos
* La evaluación sin reentrenamiento es clave para validar modelos
* La trazabilidad metodológica es tan importante como el resultado

---

## ⚠️ Qué debes tener en cuenta

* Los datos son **simulados**, no reales
* Los resultados dependen de supuestos del modelo generador
* La clasificación tiene limitaciones (recall bajo)
* No es un sistema listo para producción sin ajustes adicionales

---

## ▶️ Cómo ejecutar

### Entrenar modelos

```bash
python src/run_premodeling_modeling_.py
```

### Evaluar modelos guardados

```bash
python src/run_evaluate_saved_models.py
```

---

## 📚 Referencias base

* *An Introduction to Statistical Learning* – James et al.
* *Generalized Linear Models* – McCullagh & Nelder
* *The Elements of Statistical Learning* – Hastie et al.
* *Simulation and the Monte Carlo Method* – Rubinstein
* Azure Blob Storage Documentation

---

## 🧭 Siguiente paso recomendado

Si es tu primera vez en el proyecto:

👉 Empieza por:
**[Contexto del problema](docs/01_contexto_problema.md)**

Luego sigue el flujo completo para entender la lógica metodológica.

---

Este README actúa como **mapa del proyecto**: úsalo para orientarte antes de entrar en el detalle técnico o estadístico.
