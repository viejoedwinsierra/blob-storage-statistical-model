---
🏠 [Inicio](../README.md)

⬅️ [Anterior](06_preguntas_analiticas.md)

➡️ [Siguiente](08_validaciones_calidad.md)

---

# 7. Limitaciones y consideraciones metodológicas

## 7.1 Naturaleza sintética del dataset

El modelo se basa en simulación controlada y no en datos productivos reales.  
Esto implica:

- No se capturan patrones específicos de negocio.
- No se modelan dependencias organizacionales reales.
- No se consideran restricciones regulatorias o políticas de retención.

Sin embargo, la simulación garantiza control del fenómeno y reproducibilidad experimental.

---

## 7.2 Supuesto de independencia diaria

El modelo asume que:

- Los días son independientes entre sí.
- El estado del sistema \(I_t\) sigue un proceso Bernoulli sin memoria.

En entornos reales, los incidentes pueden presentar:

- Persistencia temporal.
- Correlación entre días consecutivos.
- Efectos de recuperación gradual.

Extensión posible:  
Modelar \(I_t\) como un proceso de Markov o incluir autocorrelación.

---

## 7.3 Supuesto de independencia entre blobs

Se asume que cada blob generado en un día tiene probabilidad independiente \(p_t\) de ser duplicado.

En sistemas reales pueden existir:

- Dependencias por lote.
- Reintentos masivos simultáneos.
- Eventos correlacionados intra-día.

Este supuesto simplifica el análisis pero puede subestimar la varianza real.

---

## 7.4 Posible sobredispersión

El modelo Poisson asume:

\[
Var(X_t) = E[X_t]
\]

En sistemas distribuidos reales puede observarse:

\[
Var(X_t) > E[X_t]
\]

debido a:

- Burst patterns.
- Congestión de red.
- Eventos coordinados.

Mitigación:  
Uso de modelo Binomial Negativa si se detecta sobredispersión.

---

## 7.5 Sensibilidad al factor k

El parámetro \(k\) controla la magnitud de los picos.

- Valores bajos pueden subestimar impacto.
- Valores altos pueden sobreestimar efectos.

Se recomienda análisis de sensibilidad variando \(k \in [2,8]\).

---

## 7.6 Ambigüedad entre duplicación lógica y física

El modelo asume que:

- Duplicación por contenido implica redundancia física.

En sistemas reales pueden existir:

- Compresión.
- Deduplicación automática en backend.
- Versionado interno.

Esto puede reducir impacto real en almacenamiento físico.

---

## 7.7 Uso del hash como variable probabilística de persistencia

El modelo incorpora variables derivadas del hash del contenido (`hash_head` y `hash_tail`) no como identificadores determinísticos absolutos, sino como una aproximación probabilística a la **persistencia y recurrencia del contenido en el sistema**.

En este contexto, el hash permite inferir:

- probabilidad de repetición de contenido
- patrones de duplicación lógica
- persistencia de estructuras de información a lo largo del tiempo

Sin embargo, este enfoque introduce limitaciones importantes:

- Alta cardinalidad ⚠️: los valores de hash tienden a ser únicos, lo que dificulta su uso directo en modelos estadísticos tradicionales.
- Naturaleza no interpretable: los hashes no contienen significado semántico explícito.
- Sensibilidad extrema: pequeñas variaciones en el contenido generan valores completamente diferentes.
- Representación indirecta: el hash no mide directamente persistencia, sino que actúa como proxy probabilístico.

Por tanto, el hash no debe interpretarse como variable explicativa directa del fenómeno económico (costo), sino como una variable auxiliar para análisis estructural del sistema.

---

## 7.8 Exclusión del efecto de compresión y optimización de almacenamiento

El modelo no incorpora mecanismos reales de optimización del almacenamiento, tales como:

- compresión de archivos
- deduplicación a nivel de infraestructura
- optimización de bloques o almacenamiento incremental

Esto implica que:

- el costo modelado corresponde a almacenamiento bruto (raw storage)
- se puede sobreestimar el volumen físico real
- no se captura la eficiencia interna del sistema de almacenamiento

Esta simplificación es intencional para mantener:

- claridad en la relación entre variables
- trazabilidad del modelo
- control del fenómeno simulado

---

## 7.9 Uso del hash en machine learning y detección de duplicados

El uso principal de las variables de hash dentro del modelo se orienta a:

- entrenamiento de modelos de machine learning
- detección de duplicados antes del almacenamiento
- identificación de patrones de contenido repetido

En este sentido, el hash cumple un rol diferente al del modelamiento estadístico:

- no se utiliza directamente en regresión del costo
- se emplea en tareas de clasificación y detección
- permite construir variables derivadas (frecuencia, recurrencia, similitud)

Este enfoque permite separar:

- análisis económico (costo de almacenamiento)
- análisis estructural (duplicidad y calidad de datos)

lo cual mejora la modularidad del modelo.

---

## 7.10 Implicaciones metodológicas del uso del hash

El tratamiento del hash dentro del modelo implica:

- considerar su uso como variable de ingeniería de características (feature engineering)
- evitar su inclusión directa en modelos lineales sin transformación
- reconocer su valor en modelos no lineales (árboles, clustering, detección de anomalías)

Esto refuerza la idea de que:

- el modelo estadístico y el modelo de machine learning cumplen roles complementarios
- el hash es más relevante para aprendizaje automático que para inferencia clásica

---

## 7.11 Limitaciones temporales

El modelo opera en granularidad diaria.

No captura:

- Picos horarios.
- Micro-burst intra-minuto.
- Efectos de ventana móvil.

Extensión futura:  
Modelamiento por hora o minuto.

---

## 7.12 Consideraciones de interpretación

Los resultados del modelo deben interpretarse como:

- Evaluación estructural del riesgo.
- Estimación probabilística del impacto.
- Herramienta de análisis comparativo.

No deben considerarse como predicción exacta del comportamiento productivo.

---

## 7.13 Conclusión metodológica

El modelo prioriza:

- Claridad estructural.
- Reproducibilidad.
- Escalabilidad conceptual.
- Capacidad de extensión.

Las simplificaciones realizadas permiten:

- Enfoque analítico controlado.
- Evaluación de hipótesis contrastables.
- Comparación de escenarios.

La robustez del enfoque radica en su capacidad de adaptarse a datos reales mediante calibración de parámetros y validación empírica.
