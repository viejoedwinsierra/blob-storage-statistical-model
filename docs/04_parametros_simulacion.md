---
🏠 [Inicio](../README.md)

⬅️ [Anterior](03_estructura_dataset.md)

➡️ [Siguiente](05_modelamiento_estadistico.md)

---


# 4. Parámetros de simulación

N = 10,000  
días = 180  
λ = 1000  
k = 3  
p_ok = 0.02  
p_fail = 0.10  
5% días con falla

En días normales:
X_t ~ Poisson(1000)

En días con falla:
X_t ~ Poisson(3000)

Duplicación:
D_t ~ Binomial(X_t, p_t)
