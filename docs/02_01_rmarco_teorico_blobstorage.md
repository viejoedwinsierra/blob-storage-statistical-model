---
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](03_00_estructura_dataset.md)

---
# 3. Marco teórico

## 3.1 Big Data y Data Engineering

El concepto de Big Data se refiere al manejo de grandes volúmenes de datos caracterizados por las conocidas *5V*: volumen, velocidad, variedad, veracidad y valor (Laney, 2001). En entornos cloud, estos datos son generados por sistemas distribuidos que producen flujos continuos de información.

En arquitecturas modernas, el almacenamiento de objetos (Object Storage) —como AWS S3 y Azure Blob Storage— actúa como repositorio central de datos no estructurados, soportando escalabilidad horizontal prácticamente ilimitada (Kleppmann, 2017).

El Data Engineering se encarga de diseñar y operar pipelines que permiten:

* ingestión de datos
* almacenamiento distribuido
* procesamiento batch y streaming
* exposición para análisis

Un pipeline típico puede representarse como:

$$
D_{raw} \rightarrow D_{processed} \rightarrow D_{analytical}
$$

donde:

* $D_{raw}$: datos crudos
* $D_{processed}$: datos transformados
* $D_{analytical}$: datos listos para modelado

En este proyecto, el pipeline es simulado, lo que permite controlar las variables del sistema y evaluar su comportamiento sin depender de entornos productivos.

---

## 3.2 Fundamentos probabilísticos

El comportamiento de los sistemas distribuidos puede modelarse mediante herramientas de probabilidad, especialmente en lo referente a la llegada de eventos y generación de artefactos.

### 3.2.1 Proceso de Poisson

La llegada de eventos se modela como:

$$
X_t \sim \text{Poisson}(\lambda)
$$

donde:

* $X_t$: número de eventos en el tiempo $t$
* $\lambda$: tasa promedio

Este modelo es apropiado para eventos independientes en el tiempo (Ross, 2014).

---

### 3.2.2 Modelo de duplicidad

La ocurrencia de errores o duplicados puede modelarse como:

$$
D_t \sim \text{Binomial}(X_t, p_{fail})
$$

donde:

* $D_t$: número de eventos con error
* $p_{fail}$: probabilidad de falla

Este modelo permite cuantificar la proporción de eventos afectados (Casella & Berger, 2002).

---

### 3.2.3 Cambio de régimen

El sistema puede alternar entre estados:

$$
incident_t \sim \text{Bernoulli}(p_{incident})
$$

y:

$$
\lambda_t =
\begin{cases}
\lambda & \text{si } incident_t = 0 \
k \cdot \lambda & \text{si } incident_t = 1
\end{cases}
$$

Esto modela sistemas con comportamiento dinámico bajo condiciones de falla (Hamilton, 1994).

---

## 3.3 Modelos estadísticos

El modelado estadístico permite explicar y predecir el comportamiento del sistema.

### 3.3.1 Regresión lineal

$$
Y = \beta_0 + \beta_1 X + \epsilon
$$

donde:

* $Y$: variable respuesta (ej. volumen de almacenamiento)
* $X$: variable explicativa (ej. número de transacciones)
* $\epsilon$: error

Este modelo permite identificar tendencias y relaciones lineales.

---

### 3.3.2 Regresión logística

Para modelar la probabilidad de error:

$$
P(Y=1|X) = \frac{1}{1 + e^{-z}}
$$

$$
z = \beta_0 + \sum_{i=1}^{n} \beta_i X_i
$$

Este modelo es fundamental para clasificación binaria y permite interpretar el impacto de cada variable (Hosmer et al., 2013).

---

### 3.3.3 Modelos de conteo

Para datos discretos:

* Poisson Regression
* Negative Binomial

Estos modelos permiten capturar sobre-dispersión en los datos.

---

## 3.4 Machine Learning

El Machine Learning permite capturar relaciones complejas no lineales entre variables.

### 3.4.1 Aprendizaje supervisado

Se busca modelar:

$$
Y = f(X)
$$

donde:

* $X$: conjunto de variables observables
* $Y$: variable objetivo (error/no error)

Algoritmos relevantes:

* Random Forest
* Gradient Boosting
* XGBoost

Estos modelos capturan interacciones complejas y no linealidades (Hastie et al., 2009).

---

### 3.4.2 Detección de anomalías

Permite identificar patrones inusuales sin etiquetas:

$$
X_i \notin \mathcal{D}_{normal}
$$

Métodos:

* Isolation Forest
* Clustering
* Autoencoders

Esto es relevante para detectar comportamientos no esperados en almacenamiento.

---

### 3.4.3 Transferencia simulación–realidad

Uno de los principales retos es evaluar:

$$
\text{Generalización} = f_{sim}(X) \rightarrow f_{real}(X)
$$

El objetivo es determinar si los patrones aprendidos en datos simulados son transferibles a sistemas reales.

---

## 3.5 Integración con el sistema simulado

El sistema puede representarse como un modelo dinámico:

$$
x_{t+1} = f(x_t, u_t, w_t)
$$

donde:

* $x_t$: estado del sistema
* $u_t$: decisiones (optimización)
* $w_t$: incertidumbre

El costo acumulado se modela como:

$$
J_T = \mathbb{E} \left[ \sum_{t=1}^{T} C(x_t, u_t, w_t) \right]
$$

Este modelo permite integrar:

* simulación
* estadística
* machine learning
* optimización

---

## Referencias

* Casella, G., & Berger, R. (2002). *Statistical Inference*.
* Hamilton, J. (1994). *Time Series Analysis*.
* Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning*.
* Hosmer, D., Lemeshow, S., & Sturdivant, R. (2013). *Applied Logistic Regression*.
* Kleppmann, M. (2017). *Designing Data-Intensive Applications*.
* Laney, D. (2001). 3D Data Management.
* Ross, S. (2014). *Introduction to Probability Models*.
