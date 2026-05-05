🏠 [Inicio](../README.md)
➡️ [Siguiente](02_00_procedencia_fuente.md)

---

# 1. Contexto del problema, fundamentación teórica e hipótesis de investigación

## 1.1 Contexto del problema

En sistemas de almacenamiento tipo *Object Storage*, como Azure Blob Storage, cada archivo almacenado constituye una unidad elemental de observación del comportamiento del sistema. A diferencia de enfoques agregados centrados en métricas globales (por ejemplo, throughput total o consumo agregado), este trabajo adopta una perspectiva **micro-analítica**, donde cada archivo representa una instancia con características propias.

Desde esta perspectiva, el comportamiento del sistema no depende únicamente del volumen de operaciones, sino de la interacción entre múltiples factores a nivel de archivo, tales como:

* tamaño del archivo
* tiempo de permanencia en almacenamiento
* tipo de almacenamiento asignado (*storage tier*)
* condiciones operativas (errores, reintentos)
* características del contenido (tipo de archivo, patrones de duplicidad)

Este enfoque permite modelar el sistema como un conjunto de realizaciones de variables aleatorias, donde el comportamiento global emerge de la agregación de comportamientos individuales.

---

## 1.2 Fundamentación teórica

El problema se fundamenta en tres pilares teóricos principales:

### 1.2.1 Procesos estocásticos y generación de datos

La llegada de archivos al sistema puede modelarse como un **proceso de Poisson**, ampliamente utilizado para describir eventos independientes en el tiempo (Ross, 2014). Bajo este supuesto, cada archivo representa una realización de un proceso de llegada, lo cual justifica el uso de simulación Monte Carlo para generar datasets sintéticos.

Por su parte, la ocurrencia de errores se modela como una variable Bernoulli:

$$
error \sim \text{Bernoulli}(p)
$$

lo cual es consistente con la teoría de probabilidad para eventos binarios (Casella & Berger, 2002).

---

### 1.2.2 Modelamiento estadístico y variables aleatorias

El sistema puede representarse mediante un conjunto de variables aleatorias:

$$
X = { size_{gb}, days_{stored}, storage_{tier}, file_{type}, error }
$$

y variables respuesta:

$$
Y = { storage_{cost}, transfer_duration, has_error }
$$

El objetivo es estimar la distribución condicional:

$$
P(Y \mid X)
$$

lo cual se enmarca en el paradigma de modelamiento supervisado (Hastie et al., 2009).

---

### 1.2.3 Sistemas distribuidos y almacenamiento

Desde la perspectiva de sistemas distribuidos, fenómenos como duplicidad de archivos y errores están relacionados con:

* problemas de **idempotencia**
* mecanismos de **reintento**
* consistencia eventual

(Kleppmann, 2017; Beyer et al., 2016).

Adicionalmente, la detección de duplicados se vincula con técnicas de deduplicación de contenido (Meyer & Bolosky, 2011), lo cual introduce complejidad adicional en el modelamiento.

---

## 1.3 Enfoque probabilístico del problema

El sistema se modela como un proceso probabilístico donde cada archivo es una observación independiente (bajo supuestos de simulación), con variables distribuidas según funciones específicas:

$$
size_{gb} \sim F_s
$$

$$
days_{stored} \sim F_t
$$

$$
storage_{tier} \sim \text{Categorical}
$$

$$
error \sim \text{Bernoulli}(p)
$$

Estas variables interactúan para generar resultados observables:

* costo de almacenamiento
* duración de transferencia
* ocurrencia de errores

Formalmente:

$$
Y = f(X) + \epsilon
$$

donde ( \epsilon ) representa el componente aleatorio no explicado.

---

## 1.4 Problema de investigación

El problema central se puede formular como:

> **¿Cómo modelar y explicar el comportamiento de un sistema de almacenamiento tipo Blob Storage a partir de variables observables a nivel de archivo bajo condiciones de incertidumbre?**

Esto implica abordar subproblemas como:

* identificación de variables relevantes
* caracterización de distribuciones
* modelamiento de relaciones entre variables
* capacidad predictiva de modelos estadísticos

---

## 1.5 Hipótesis de investigación

A partir del marco teórico y la estructura del problema, se plantean las siguientes hipótesis:

---

### Hipótesis 1 (H1): Relación estructural en duración

**H1:** La duración de transferencia sigue una relación multiplicativa con las variables explicativas, por lo que un modelo log-lineal captura adecuadamente su comportamiento.

**Justificación:**
La presencia de escalas variables (tamaño, tiempo, condiciones operativas) sugiere efectos proporcionales más que aditivos, lo cual es consistente con modelos log-lineales (Wooldridge, 2010).

---

### Hipótesis 2 (H2): Distribución del costo

**H2:** El costo de almacenamiento sigue una distribución asimétrica positiva que es mejor modelada mediante un GLM con distribución Gamma que mediante un modelo lineal clásico.

**Justificación:**
El costo es una variable continua, positiva y con varianza dependiente de la media, características propias de la familia Gamma (McCullagh & Nelder, 1989).

---

### Hipótesis 3 (H3): Capacidad predictiva en clasificación

**H3:** La ocurrencia de errores puede modelarse mediante regresión logística con capacidad discriminativa adecuada, aunque limitada en recall debido a desbalance de clases.

**Justificación:**
Los eventos de error suelen ser raros en sistemas distribuidos, lo que afecta la sensibilidad del modelo (James et al., 2013).

---

### Hipótesis 4 (H4): Generalización del modelo

**H4:** Los modelos entrenados sobre datos simulados mediante Monte Carlo generalizan adecuadamente a nuevos datasets generados bajo las mismas condiciones.

**Justificación:**
Si el proceso generador es estacionario, los patrones aprendidos deben mantenerse (Rubinstein & Kroese, 2016).

---

### Hipótesis 5 (H5): Suficiencia de modelos interpretables

**H5:** Modelos estadísticos clásicos (OLS, GLM, Logístico) son suficientes para capturar la mayor parte de la variabilidad del sistema sin necesidad de modelos de machine learning complejos.

**Justificación:**
Cuando la relación entre variables está bien especificada, modelos paramétricos pueden ser altamente efectivos (Hastie et al., 2009).

---

## 1.6 Alcance del estudio

El presente trabajo se enfoca en:

* modelamiento estructural del sistema
* análisis probabilístico controlado
* evaluación de modelos en datasets simulados

No busca:

* replicar completamente sistemas reales
* capturar todos los factores operativos
* sustituir sistemas productivos

---

## 1.7 Contribución del trabajo

Este estudio contribuye mediante:

* un enfoque probabilístico a nivel de archivo
* un dataset sintético estructurado para análisis
* un pipeline completo de modelamiento estadístico
* evaluación de generalización sin reentrenamiento

---

## 1.9 Referencias

* Beyer, B., Jones, C., Petoff, J., & Murphy, N. (2016). *Site Reliability Engineering*.
* Casella, G., & Berger, R. (2002). *Statistical Inference*.
* Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning*.
* James, G., Witten, D., Hastie, T., & Tibshirani, R. (2013). *An Introduction to Statistical Learning*.
* Kleppmann, M. (2017). *Designing Data-Intensive Applications*.
* McCullagh, P., & Nelder, J. (1989). *Generalized Linear Models*.
* Meyer, D., & Bolosky, W. (2011). *A Study of Practical Deduplication*.
* Ross, S. (2014). *Introduction to Probability Models*.
* Rubinstein, R., & Kroese, D. (2016). *Simulation and the Monte Carlo Method*.
* Wooldridge, J. (2010). *Econometric Analysis of Cross Section and Panel Data*.

---
