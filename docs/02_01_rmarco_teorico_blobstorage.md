🏠 [Inicio](../README.md)
⬅️ [Anterior](02_00_procedencia_fuente.md)
➡️ [Siguiente](03_00_estructura_dataset.md)

---

# 3. Marco teórico ampliado: evolución hacia cloud, almacenamiento y facturación

## 3.1 Transformación digital: de on-premise a cloud

En las últimas dos décadas, las organizaciones han transitado de arquitecturas **on-premise** hacia modelos **cloud computing**, motivadas por la necesidad de:

* escalabilidad bajo demanda
* reducción de costos de infraestructura
* elasticidad operativa
* acceso global a datos

En entornos tradicionales *on-premise*, las empresas debían:

* invertir en infraestructura física (CAPEX)
* dimensionar capacidad anticipadamente
* asumir costos fijos independientemente del uso

En contraste, el modelo cloud introduce un esquema **pay-as-you-go (OPEX)**, donde el costo depende directamente del consumo real de recursos (Armbrust et al., 2010).

---

## 3.2 Object Storage como núcleo del ecosistema de datos

Dentro de la arquitectura cloud, el almacenamiento de objetos (Object Storage), como Azure Blob Storage o Amazon S3, se ha convertido en el componente central para datos no estructurados.

Sus características principales incluyen:

* almacenamiento masivo y distribuido
* durabilidad alta (≥ 99.999999999%)
* acceso basado en objetos
* separación entre cómputo y almacenamiento

(Kleppmann, 2017)

En este contexto, cada archivo almacenado representa una unidad económica y operativa, lo que justifica el enfoque micro-analítico adoptado en este trabajo.

---

## 3.3 Problema real en empresas: pérdida de control del costo

Uno de los principales problemas que enfrentan las organizaciones al migrar a cloud es:

> **la dificultad para controlar, predecir y optimizar los costos de almacenamiento**

Este problema surge por múltiples factores:

### 3.3.1 Facturación basada en múltiples dimensiones

El costo en sistemas como Blob Storage depende de:

* volumen almacenado (GB/mes)
* tipo de almacenamiento (hot, cool, archive)
* número de operaciones (lectura/escritura)
* transferencia de datos (egress)
* redundancia (LRS, GRS, etc.)

Esto genera una estructura de costo:

$$
Cost = f(size, time, tier, operations, transfer)
$$

que es inherentemente **no lineal y difícil de estimar manualmente**.

---

### 3.3.2 Crecimiento no controlado de datos

Las organizaciones suelen enfrentar:

* acumulación de datos históricos
* duplicidad de archivos
* falta de políticas de lifecycle management
* almacenamiento de datos innecesarios

Esto genera fenómenos como:

* **costos ocultos**
* almacenamiento ineficiente
* aumento progresivo del gasto

---

### 3.3.3 Falta de visibilidad a nivel granular

En muchos casos, las empresas solo observan:

* costo total mensual

pero no:

* costo por archivo
* costo por tipo de dato
* costo por unidad de negocio

Esto limita la capacidad de optimización.

---

## 3.4 Relación con el problema abordado en este trabajo

El presente estudio aborda directamente este problema mediante:

* modelamiento a nivel de archivo
* simulación de escenarios operativos
* estimación del costo en función de variables observables

En particular, se busca modelar:

$$
P(storage_cost \mid X)
$$

donde:

$$
X = { size_{gb}, days_{stored}, storage_{tier}, error }
$$

Esto permite responder preguntas clave para la industria:

* ¿Qué variables explican el costo?
* ¿Cómo optimizar almacenamiento?
* ¿Dónde se generan ineficiencias?

---

## 3.5 Fundamentos probabilísticos del sistema

El comportamiento del sistema se modela mediante variables aleatorias:

### Llegada de datos

$$
X_t \sim \text{Poisson}(\lambda)
$$

representando la generación de archivos en el tiempo (Ross, 2014).

---

### Errores y duplicidad

$$
error \sim \text{Bernoulli}(p)
$$

$$
duplicates \sim \text{Binomial}(n, p)
$$

lo cual permite modelar calidad del sistema (Casella & Berger, 2002).

---

### Cambio de régimen

Los sistemas reales presentan estados:

* operación normal
* degradación
* incidentes

Esto se modela como:

$$
incident_t \sim \text{Bernoulli}(p_{incident})
$$

(Hamilton, 1994)

---

## 3.6 Modelamiento estadístico aplicado al problema

El uso de modelos estadísticos permite transformar el problema de negocio en un problema cuantificable.

### Regresión para costo

El costo se modela como:

$$
Y = f(X)
$$

donde, debido a su naturaleza:

* positiva
* continua
* asimétrica

es apropiado el uso de modelos como:

* GLM Gamma
* modelos log-lineales

(McCullagh & Nelder, 1989)

---

### Modelamiento de duración

La duración de transferencia sigue relaciones multiplicativas:

$$
\log(Y) = \beta_0 + \beta X + \epsilon
$$

lo que justifica el uso de modelos log-lineales.

---

### Clasificación de errores

La probabilidad de error se modela como:

$$
P(Y=1|X) = \frac{1}{1 + e^{-z}}
$$

(Hosmer et al., 2013)

---

## 3.7 Rol de la simulación en el contexto empresarial

El uso de simulación (ver sección 2) permite:

* replicar escenarios que no son accesibles en producción
* evaluar estrategias de optimización
* analizar impacto de decisiones

Esto es clave porque en entornos reales:

* experimentar directamente puede ser costoso
* los datos pueden ser incompletos
* los cambios pueden afectar producción

---

## 3.8 Relación con el sistema simulado

El sistema puede representarse como:

$$
x_{t+1} = f(x_t, u_t, w_t)
$$

donde:

* $x_t$: estado del almacenamiento
* $u_t$: decisiones (tiering, eliminación)
* $w_t$: incertidumbre

El costo acumulado:

$$
J_T = \mathbb{E} \left[ \sum_{t=1}^{T} C(x_t) \right]
$$

representa el gasto total del sistema.

---

## 3.9 Implicaciones prácticas para empresas

El enfoque desarrollado permite:

* estimar costos antes de implementarlos
* diseñar políticas de almacenamiento
* detectar ineficiencias
* simular escenarios de crecimiento

Esto es especialmente relevante en contextos de:

* migración a cloud
* optimización de costos
* gobierno de datos

---

## Referencias

* Armbrust, M. et al. (2010). *A View of Cloud Computing*.
* Casella, G., & Berger, R. (2002). *Statistical Inference*.
* Hamilton, J. (1994). *Time Series Analysis*.
* Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning*.
* Hosmer, D. et al. (2013). *Applied Logistic Regression*.
* Kleppmann, M. (2017). *Designing Data-Intensive Applications*.
* Laney, D. (2001). *3D Data Management*.
* McCullagh, P., & Nelder, J. (1989). *Generalized Linear Models*.
* Ross, S. (2014). *Introduction to Probability Models*.

---
