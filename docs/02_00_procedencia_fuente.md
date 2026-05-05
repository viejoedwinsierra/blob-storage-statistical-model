🏠 [Inicio](../README.md)
⬅️ [Anterior](01_contexto_problema.md)
➡️ [Siguiente](02_01_rmarco_teorico_blobstorage.md)

---

# 2. Procedencia y fuente de la base de datos

## 2.1 Enfoque general: simulación como fuente de datos

Debido a las limitaciones de acceso a datos reales completos en entornos productivos de almacenamiento distribuido, este trabajo adopta un enfoque de **simulación controlada**, el cual permite construir un entorno experimental reproducible donde las condiciones del sistema pueden ser parametrizadas, modificadas y evaluadas de manera sistemática.

A diferencia de datasets observacionales, este enfoque permite:

* controlar las distribuciones de las variables
* aislar efectos específicos
* generar múltiples escenarios experimentales
* validar hipótesis bajo condiciones reproducibles

---

## 2.2 Repositorio de simulación y trazabilidad del proceso

La generación de datos se implementa mediante un repositorio de simulación desarrollado específicamente para este proyecto:

🔗 **Repositorio:**
https://github.com/viejoedwinsierra/mvp_thesis_simulator

Este repositorio constituye el núcleo del proceso generativo y permite:

* definir parámetros de simulación configurables
* ejecutar múltiples corridas del experimento
* generar datasets independientes bajo distintas condiciones
* soportar diferentes casos de uso analítico

Desde una perspectiva metodológica, este repositorio actúa como un **generador de datos basado en Monte Carlo**, donde cada ejecución produce una realización del sistema bajo un conjunto de parámetros definidos.

---

## 2.3 Simulación Monte Carlo y generación de escenarios

El simulador implementa un esquema de **simulación Monte Carlo**, en el cual:

* las variables de entrada se definen mediante distribuciones probabilísticas
* se generan múltiples observaciones independientes
* se construyen datasets sintéticos replicables

Formalmente, cada ejecución del simulador produce un dataset:

$$
\mathcal{D}^{(k)} = {u_i^{(k)}}_{i=1}^{N}
$$

donde:

* (k) representa una corrida de simulación
* (N) es el número de archivos generados
* cada (u_i) es una instancia del sistema

Esto permite no solo analizar un dataset, sino estudiar la **variabilidad entre datasets**, fortaleciendo el análisis de generalización.

---

## 2.4 Parametrización del sistema

El repositorio permite configurar dinámicamente parámetros como:

* número de archivos generados
* distribución del tamaño (`LogNormal`)
* distribución del tiempo (`Uniforme`, `Exponencial`)
* probabilidades de error
* proporciones de tipos de archivo
* niveles de almacenamiento

Esto convierte el simulador en un **entorno de experimentación controlada**, donde se pueden probar distintos escenarios:

* alta tasa de errores
* crecimiento acelerado de datos
* cambios en patrones de acceso
* variaciones en distribución de tamaños

---

## 2.5 Generación del universo de contenidos

Se define un conjunto inicial de archivos:

$$
N = 10{,}000
$$

Cada archivo representa una unidad observable del sistema y se caracteriza por variables como:

* `file_id`
* `file_type`
* `size_gb`
* `creation_timestamp`

El tamaño se modela como:

$$
size_{gb} \sim \text{LogNormal}(\mu, \sigma)
$$

lo cual captura la naturaleza realista de sistemas de almacenamiento:

* alta frecuencia de archivos pequeños
* presencia de archivos grandes (cola pesada)

---

## 2.6 Generación de variables estructurales

Las variables principales se generan como:

$$
file_{type} \sim \text{Categorical}
$$

$$
days_{stored} \sim F_t
$$

$$
storage_{tier} \sim \text{Categorical}
$$

Estas variables representan:

* estructura del sistema
* comportamiento del ciclo de vida
* estrategia de almacenamiento

---

## 2.7 Variables operativas y dependencias

El simulador introduce dependencias estructurales:

$$
transfer_{speed} = \frac{size_{gb}}{transfer_{duration}}
$$

lo cual permite:

* modelar eficiencia
* capturar relaciones funcionales
* evitar independencia artificial entre variables

---

## 2.8 Generación de errores

Los errores se modelan como:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

incluyendo:

* errores de duplicidad
* errores de integridad
* errores de transferencia

Esto permite estudiar la calidad del sistema bajo distintas condiciones.

---

## 2.9 Representación de contenido mediante hash parcial

Para optimizar el costo computacional, el simulador utiliza:

$$
hash_{head}, \quad hash_{tail}
$$

como aproximaciones del contenido.

Este enfoque permite:

* estimar duplicidad
* identificar recurrencia
* reducir complejidad computacional

aunque introduce incertidumbre en la identificación exacta.

---

## 2.10 Construcción del dataset maestro

Cada ejecución del simulador genera un dataset:

$$
\mathcal{D} = {u_i}_{i=1}^{N}
$$

donde cada instancia contiene:

* variables estructurales
* variables temporales
* variables operativas
* variables de error
* proxies de contenido

Este dataset es utilizado para:

* análisis exploratorio
* modelamiento estadístico
* validación de hipótesis

---

## 2.11 Relación con el modelo probabilístico

El sistema se modela como:

$$
X = { size_{gb}, file_{type}, days_{stored}, storage_{tier}, error }
$$

$$
Y = { storage_{cost}, transfer_duration, has_error }
$$

y el objetivo es estimar:

$$
P(Y \mid X)
$$

El simulador permite generar múltiples realizaciones de (X), lo cual fortalece la estimación de esta relación.

---

## 2.12 Rol del simulador en la validación de hipótesis

El repositorio no solo genera datos, sino que habilita:

* validación de hipótesis bajo distintos escenarios
* análisis de sensibilidad
* evaluación de estabilidad de modelos

Esto lo convierte en una herramienta clave para:

* verificar consistencia de resultados
* analizar robustez del modelamiento
* estudiar generalización

---

## 2.13 Limitaciones del enfoque

A pesar de sus ventajas, el enfoque presenta limitaciones:

* dependencia de supuestos de simulación
* simplificación de dependencias reales
* ausencia de fenómenos no modelados (ej. latencias de red complejas)
* uso de hash parcial

Por lo tanto, los resultados deben interpretarse como **aproximaciones controladas**, no como representaciones exactas de sistemas reales.


---

## 2.15 Referencias

* Ross, S. (2014). *Introduction to Probability Models*.
* Rubinstein, R., & Kroese, D. (2016). *Simulation and the Monte Carlo Method*.
* Casella, G., & Berger, R. (2002). *Statistical Inference*.
* Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning*.
* Kleppmann, M. (2017). *Designing Data-Intensive Applications*.
* Repositorio de simulación: https://github.com/viejoedwinsierra/mvp_thesis_simulator

---
