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

## 7.7 Limitaciones en el uso de hash como variable

El modelo incorpora variables derivadas del hash del contenido (`hash_head` y `hash_tail`) como aproximación a la identidad estructural del archivo. Sin embargo, esto introduce varias limitaciones:

- Alta cardinalidad ⚠️: cada hash puede ser único, dificultando su uso directo en modelos estadísticos.
- Baja interpretabilidad: los valores del hash no tienen significado semántico directo.
- Dependencia estructural: los hashes derivan del contenido, lo que puede introducir relaciones no lineales difíciles de modelar.
- Sensibilidad a pequeñas variaciones: cambios mínimos en el contenido generan hashes completamente diferentes.
- Limitación para análisis estadístico clásico: no son adecuados para técnicas como regresión sin transformación previa.

En consecuencia, estas variables requieren:

- transformación (agrupación, hashing reducido, encoding)
- uso en análisis exploratorio más que en modelamiento directo
- interpretación como variables auxiliares, no principales

---

## 7.8 Costo computacional del procesamiento de hash

El uso de hash en sistemas reales implica:

- cálculo intensivo para grandes volúmenes de datos
- necesidad de lectura completa del archivo
- impacto en latencia en sistemas distribuidos

Para grandes volúmenes, esto requiere:

- procesamiento incremental
- uso de pipelines distribuidos (Spark, MapReduce)
- estrategias de muestreo o hashing parcial

---

## 7.9 Escalabilidad y costos computacionales

Aunque el modelo es conceptualmente escalable a terabytes:

- el procesamiento masivo requiere arquitectura distribuida
- el acceso a metadatos puede tener latencia significativa

Implementación real requeriría:

- particionamiento del dataset
- procesamiento paralelo
- almacenamiento optimizado de metadatos

---

## 7.10 Desbalance entre clases (días normales vs incidentes)

Si \(p_{incident}\) es bajo (ej. 5%):

- La mayoría de observaciones serán días normales.
- Puede generarse desbalance en modelos predictivos.

Mitigación:

- Estratificación.
- Métricas robustas (AUC, recall en clase minoritaria).
- Simulación de escenarios adicionales.

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
