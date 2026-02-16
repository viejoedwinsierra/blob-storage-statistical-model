---
🏠 [Inicio](../README.md)

⬅️ [Anterior](08_validaciones_calidad.md)

➡️ [Siguiente](10_referencias.md)

---

# 9. Modelo causal de fallas

## 9.1 Planteamiento conceptual

El modelo propuesto asume que los incidentes operativos no afectan únicamente la estabilidad del sistema, sino que alteran directamente el patrón de generación de blobs.

La relación causal se plantea como:

\[
\text{Falla} \rightarrow \uparrow TPS \rightarrow \uparrow X_t \rightarrow \uparrow D_t
\]

Donde:

- **Falla (I_t = 1)**: evento de degradación operativa (timeouts, reintentos).
- **TPS**: transacciones por segundo observadas.
- **X_t**: número total de blobs generados en el día.
- **D_t**: número de duplicados por contenido.

---

## 9.2 Mecanismo operativo

En sistemas distribuidos con reintentos automáticos:

1. Se produce un timeout o error temporal.
2. El cliente o microservicio reintenta la operación.
3. El backend puede procesar múltiples intentos de la misma transacción.
4. Se generan múltiples blobs con el mismo contenido pero diferente nombre.

Esto produce:

- Incremento artificial del volumen.
- Aumento de TPS observado.
- Mayor probabilidad de duplicación.

El fenómeno no implica mayor demanda real, sino degradación del sistema.

---

## 9.3 Formalización estadística del efecto causal

El estado del sistema se modela como:

\[
I_t \sim Bernoulli(p_{incident})
\]

El volumen diario depende del estado:

\[
E[X_t | I_t=1] = k\lambda
\]

\[
E[X_t | I_t=0] = \lambda
\]

Por lo tanto:

\[
E[X_t | I_t=1] > E[X_t | I_t=0]
\]

De manera similar, la probabilidad de duplicación cambia:

\[
p_t =
\begin{cases}
p_{ok} & I_t = 0 \\
p_{fail} & I_t = 1
\end{cases}
\]

Con:

\[
p_{fail} > p_{ok}
\]

---

## 9.4 Interpretación en términos de riesgo sistémico

El incidente actúa como variable latente que modifica simultáneamente:

- Intensidad del proceso de conteo (λ → kλ).
- Probabilidad de duplicación (p_ok → p_fail).
- Métricas derivadas (TPS, volumen acumulado).

Esto implica que el sistema experimenta un cambio estructural en su distribución generativa.

Desde una perspectiva de monitoreo, un incremento simultáneo en:

- TPS,
- Volumen diario,
- Tasa de duplicación,

puede interpretarse como señal temprana de degradación operativa.

---

## 9.5 Representación alternativa (modelo estructural simplificado)

Puede expresarse como:

\[
X_t = f(I_t, \lambda, k)
\]

\[
D_t = g(X_t, p_t)
\]

Donde:

- \(f\) representa el proceso de generación de eventos.
- \(g\) representa el proceso de duplicación condicional.

El incidente actúa como variable exógena que altera ambos mecanismos.

---

## 9.6 Implicaciones prácticas

El modelo causal permite:

- Identificar si el crecimiento es estructural o inducido por fallas.
- Separar crecimiento orgánico de crecimiento por reprocesos.
- Estimar impacto financiero de incidentes.
- Diseñar métricas de alerta temprana.

---

## 9.7 Limitación del modelo causal

El modelo asume causalidad unidireccional simplificada.  
En sistemas reales pueden existir:

- Retroalimentación (feedback loops).
- Saturación de infraestructura.
- Efectos no lineales.

Sin embargo, para fines analíticos, la estructura propuesta captura el mecanismo dominante del fenómeno estudiado.
