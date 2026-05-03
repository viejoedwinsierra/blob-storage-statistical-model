🏠 [Inicio](../README.md)

➡️ [Siguiente](02_procedencia_fuente.md)

---

# 1. Contexto del problema y propósito del análisis

En sistemas de almacenamiento tipo *Object Storage*, cada archivo almacenado representa una unidad observable del comportamiento del sistema. A diferencia de enfoques tradicionales centrados en eventos agregados, este trabajo propone un análisis a nivel de **instancia (archivo)**, donde cada observación captura características del dato, su ciclo de vida y condiciones operativas.

En este contexto, el crecimiento del almacenamiento no depende únicamente del número de eventos, sino de la interacción entre:

- el tamaño de los archivos  
- el tiempo de almacenamiento  
- el tipo de almacenamiento asignado  
- la presencia de errores o duplicidad  
- el comportamiento del ciclo de vida del dato  

---
## Marco teórico y fundamentación

El enfoque probabilístico adoptado en este trabajo se fundamenta en teorías clásicas de modelamiento estocástico y sistemas distribuidos.

La generación de archivos puede interpretarse como un proceso de llegada de eventos, comúnmente modelado mediante procesos de Poisson (Ross, 2014), donde cada evento genera un artefacto almacenado.

Las variables de error se modelan como procesos Bernoulli, lo cual es consistente con modelos probabilísticos de ocurrencia binaria (Casella & Berger, 2002).

El comportamiento del sistema bajo condiciones normales y de falla puede interpretarse como un proceso con cambio de régimen, ampliamente estudiado en modelos de series de tiempo (Hamilton, 1994).

Desde la perspectiva de sistemas distribuidos, la generación de artefactos y la aparición de duplicados se relaciona con problemas de idempotencia y reintentos en arquitecturas desacopladas (Kleppmann, 2017; Beyer et al., 2016).

Adicionalmente, la detección de duplicados basada en contenido se fundamenta en técnicas de deduplicación estudiadas en sistemas de almacenamiento (Meyer & Bolosky, 2011).

---

## Enfoque probabilístico del problema

Desde una perspectiva probabilística, el sistema puede entenderse como un conjunto de variables aleatorias a nivel de archivo:

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

Estas variables interactúan para generar resultados observables, como:

- costo de almacenamiento por archivo  
- probabilidad de error  
- probabilidad de duplicidad  
- persistencia del contenido  

El problema se puede formalizar como:

$$
Y = f(X)
$$

donde:

$$
X = \{ size_{gb}, days_{stored}, storage_{tier}, file_{type}, error \}
$$

y

$$
Y = \{ storage_{cost}, is_{duplicate}, has_{error} \}
$$

De forma más general:

$$
P(Y \mid X)
$$

representa la distribución condicional que el modelo busca estimar.

---

## Problema central

El problema fundamental consiste en **explicar y modelar el comportamiento del sistema de almacenamiento a partir de variables observables a nivel de archivo**, bajo condiciones de incertidumbre.

Esto implica responder preguntas como:

- ¿Qué variables explican el costo de almacenamiento?  
- ¿Cómo afectan los errores la calidad del sistema?  
- ¿Qué factores influyen en la persistencia del contenido?  
- ¿Qué patrones permiten detectar duplicados antes del almacenamiento?  

---

## Enfoque basado en dataset

Dado que no se dispone de datos reales completamente controlados, se construye un **dataset sintético estructurado**, donde cada fila representa un archivo con variables como:

- tamaño (`size_gb`)  
- tipo de archivo (`file_type`)  
- nivel de almacenamiento (`storage_tier`)  
- tiempo (`days_stored`)  
- errores (`error_*`)  
- segmentos de contenido (`hash_head`, `hash_tail`)  

Este dataset permite:

- aplicar análisis exploratorio de datos (EDA)  
- estudiar distribuciones de variables  
- evaluar relaciones entre variables  
- construir modelos estadísticos y de machine learning  

---

## Propósito del estudio

El objetivo del análisis es:

- Identificar variables relevantes para el modelamiento  
- Analizar el comportamiento estadístico de las variables  
- Evaluar relaciones entre variables independientes y dependientes  
- Construir modelos que permitan explicar el comportamiento del sistema  

En particular, se busca responder:

$$
\text{¿Qué variables explican el comportamiento del sistema de almacenamiento a nivel de archivo bajo incertidumbre?}
$$

---

## Relación con modelos estadísticos

El problema se enmarca en el estudio de:

- distribuciones de probabilidad  
- variables categóricas  
- variables binarias  
- relaciones entre variables  

Esto permite aplicar:

- análisis univariado  
- análisis bivariado  
- selección de variables  
- modelamiento estadístico  

---

## Alcance del modelo

El modelo se enfoca en:

- comportamiento estructural del sistema  
- relaciones entre variables observables  
- análisis probabilístico controlado  

No busca:

- replicar exactamente sistemas reales  
- modelar optimizaciones internas (compresión, deduplicación física)  

---

## Contribución del trabajo

Este trabajo aporta:

- un dataset estructurado para análisis estadístico  
- un enfoque probabilístico a nivel de archivo  
- una base para modelamiento en machine learning  
- un entorno controlado para evaluación de hipótesis  

---

## Conclusión

El análisis modela el sistema de almacenamiento como un conjunto de variables aleatorias observables, donde el comportamiento global emerge de la interacción entre características del archivo, ciclo de vida y condiciones operativas.

Este enfoque permite conectar teoría estadística con aplicación práctica, facilitando el aprendizaje y uso de modelos estadísticos en un entorno controlado.
