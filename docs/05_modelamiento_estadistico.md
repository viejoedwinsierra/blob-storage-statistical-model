---
🏠 [Inicio](../README.md)

⬅️ [Anterior](04_parametros_simulacion.md)

➡️ [Siguiente](06_preguntas_analiticas.md)

---


# 5. Modelamiento estadístico

## Modelo de conteo
X_t ~ Poisson(λ_t)

## Modelo de duplicación
D_t ~ Binomial(X_t, p_t)

## Regresión logística
log(p_t/(1-p_t)) = α0 + α1 * incident_flag

Hipótesis:
H1: E[X_t|E=1] > E[X_t|E=0]
H2: p_fail > p_ok
H3: TPS aumenta en días con falla
