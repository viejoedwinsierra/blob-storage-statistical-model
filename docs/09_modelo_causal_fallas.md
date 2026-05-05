🏠 [Inicio](../README.md)

⬅️ [Anterior](08_validaciones_calidad.md)
➡️ [Siguiente](10_referencias.md)

---

# 9. Modelo causal de fallas

---

## 9.1 Planteamiento conceptual

El modelo propuesto asume que los incidentes operativos no afectan únicamente la estabilidad del sistema, sino que alteran directamente el patrón de generación de blobs.

La relación causal se plantea como:

[
\text{Falla} \rightarrow \uparrow TPS \rightarrow \uparrow X_t \rightarrow \uparrow D_t
]

Donde:

* **Falla (I_t = 1)**: evento de degradación operativa (timeouts, reintentos).
* **TPS**: transacciones por segundo observadas.
* **X_t**: número total de blobs generados.
* **D_t**: número de duplicados.

---

## 9.2 Evidencia empírica del comportamiento

### 📊 Relación entre carga y congestión

![Carga vs congestión](../img/advanced/scatter_hourly_arrival_count_vs_congestion_factor.png)

### 🧠 Interpretación

* A mayor carga → mayor congestión
* La congestión no es lineal, presenta crecimiento acelerado

👉 Esto valida que el sistema responde dinámicamente a condiciones operativas.

---

### 📊 Relación entre duplicados y condiciones del sistema

![Relaciones error](../img/advanced/target_relationships_has_error.png)

### 🧠 Interpretación

* La probabilidad de error aumenta en condiciones adversas
* Se observa comportamiento no lineal

👉 Esto respalda la hipótesis:

[
p_{fail} > p_{ok}
]

---

## 9.3 Mecanismo operativo

En sistemas distribuidos con reintentos automáticos:

1. Se produce un timeout o error temporal.
2. El cliente o microservicio reintenta la operación.
3. El backend puede procesar múltiples intentos de la misma transacción.
4. Se generan múltiples blobs con el mismo contenido.

Esto produce:

* incremento artificial del volumen
* aumento de TPS
* incremento de duplicados
* aumento de costos indirectos

---

## 9.4 Formalización estadística del efecto causal

El estado del sistema se modela como:

[
I_t \sim Bernoulli(p_{incident})
]

El volumen depende del estado:

[
E[X_t | I_t=1] = k\lambda
]

[
E[X_t | I_t=0] = \lambda
]

Por lo tanto:

[
E[X_t | I_t=1] > E[X_t | I_t=0]
]

La probabilidad de duplicación:

[
p_t =
\begin{cases}
p_{ok} & I_t = 0 \
p_{fail} & I_t = 1
\end{cases}
]

---

## 9.5 Evidencia desde modelamiento y evaluación

### 📊 Distribución de probabilidades de error

![Distribución probabilidades](../img/model_evaluation/evaluation_probability_distribution_has_error_logistic_regression.png)

### 🧠 Interpretación

* Alta concentración en probabilidades bajas
* Pocos eventos extremos

👉 Esto confirma:

* errores raros
* pero sensibles a condiciones del sistema

---

### 📊 Curva ROC (estabilidad del fenómeno)

![ROC evaluación](../img/model_evaluation/evaluation_roc_curve_has_error_logistic_regression.png)

### 🧠 Interpretación

* El modelo distingue correctamente eventos
* pero no captura todos los positivos

👉 Relación directa con:

[
p_{fail} \neq p_{ok}
]

---

## 9.6 Interpretación en términos de riesgo sistémico

El incidente actúa como variable latente que modifica simultáneamente:

* intensidad del proceso (λ → kλ)
* probabilidad de error (p_ok → p_fail)
* métricas derivadas

Esto implica un **cambio estructural en la distribución del sistema**.

---

## 9.7 Señales observables del fenómeno

El modelo permite identificar señales tempranas de degradación:

| Métrica               | Comportamiento esperado  |
| --------------------- | ------------------------ |
| TPS                   | Incremento anormal       |
| Volumen (`X_t`)       | Crecimiento no explicado |
| Duplicados (`D_t`)    | Incremento sostenido     |
| Probabilidad de error | Aumento en colas altas   |

👉 Estas señales combinadas son más informativas que una métrica aislada.

---

## 9.8 Representación estructural

[
X_t = f(I_t, \lambda, k)
]

[
D_t = g(X_t, p_t)
]

Donde:

* (I_t): variable exógena (incidente)
* (X_t): volumen generado
* (D_t): duplicación

---

## 9.9 Relación con el simulador

El modelo causal es consistente con el simulador porque:

* el simulador define estados (normal vs incidente)
* modifica probabilidades y volumen
* genera datos coherentes con ese proceso

👉 Esto hace que el modelo no sea arbitrario:

> Está alineado con el proceso generativo de los datos.

---

## 9.10 Implicaciones prácticas

El modelo permite:

* diferenciar crecimiento real vs inducido por fallas
* estimar impacto económico de incidentes
* diseñar alertas tempranas
* detectar degradación antes de fallas críticas

---

## 9.11 Limitaciones del modelo causal

El modelo asume causalidad simplificada:

[
I_t \rightarrow X_t \rightarrow D_t
]

Pero en sistemas reales pueden existir:

* feedback loops
* saturación
* efectos acumulativos
* dependencia temporal

---

## 9.12 Conclusión

El modelo causal propuesto permite explicar cómo eventos de falla generan efectos observables en el sistema:

* aumento de volumen
* incremento de duplicados
* cambios en métricas operativas

Además, conecta de forma consistente:

```text
Simulación → Datos → Modelos → Evidencia → Causalidad
```

Esto fortalece el proyecto porque no solo modela el sistema, sino que explica su comportamiento bajo condiciones de falla.

---
