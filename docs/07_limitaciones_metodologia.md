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

El parámetro k controla la magnitud de los picos.

- Valores bajos pueden subestimar impacto.
- Valores altos pueden sobreestimar efectos.

Se recomienda análisis de sensibilidad variando k ∈ [2,8].

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

## 7.7 Escalabilidad y costos computacionales

Aunque el modelo es conceptualmente escalable a terabytes:

- El cálculo de hash completo puede ser costoso.
- El procesamiento masivo requiere arquitectura distribuida.
- El acceso a metadatos puede tener latencia significativa.

Implementación real requeriría:

- Procesamiento incremental.
- Uso de particiones.
- Agregación distribuida (Spark / MapReduce).

---

## 7.8 Desbalance entre clases (días normales vs incidentes)

Si \(p_{incident}\) es bajo (ej. 5%):

- La mayoría de observaciones serán días normales.
- Puede generarse desbalance en modelos predictivos.

Mitigación:

- Estratificación.
- Métricas robustas (AUC, recall en clase minoritaria).
- Simulación de escenarios adicionales.

---

## 7.9 Limitaciones temporales

El modelo opera en granularidad diaria.

No captura:

- Picos horarios.
- Micro-burst intra-minuto.
- Efectos de ventana móvil.

Extensión futura:
Modelamiento por hora o minuto.

---

## 7.10 Consideraciones de interpretación

Los resultados del modelo deben interpretarse como:

- Evaluación estructural del riesgo.
- Estimación probabilística del impacto.
- Herramienta de análisis comparativo.

No deben considerarse como predicción exacta del comportamiento productivo.

---

## 7.11 Conclusión metodológica

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
