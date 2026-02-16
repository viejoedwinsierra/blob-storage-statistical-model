---
🏠 [Inicio](../README.md)

⬅️ [Anterior](07_limitaciones_metodologia.md)

➡️ [Siguiente](09_modelo_causal_fallas.md)

---

# 8. Validaciones de calidad

## 8.1 Marco conceptual de calidad de datos

La calidad del dataset se evalúa bajo cuatro dimensiones fundamentales:

1. **Integridad estructural**
2. **Consistencia lógica**
3. **Coherencia estadística**
4. **Reproducibilidad del proceso generativo**

Estas dimensiones garantizan que el modelo estadístico opere sobre datos válidos y coherentes.

---

## 8.2 Validaciones estructurales (nivel blob)

Se verifican condiciones mínimas de integridad:

- `size_bytes >= 0`
- `content_hash` no nulo
- `blob_id` único
- `created_at <= last_modified_at`
- `path` válido y consistente con la estructura jerárquica definida

Objetivo:
Evitar inconsistencias que comprometan el análisis probabilístico.

---

## 8.3 Validaciones de unicidad y duplicación

Se evalúan tres niveles de control:

### 1️⃣ Unicidad técnica
- No deben existir duplicados exactos de `(blob_id)`.

### 2️⃣ Duplicación por contenido
- Identificación de grupos donde `content_hash` se repite.
- Cálculo de cardinalidad por hash.

### 3️⃣ Duplicación cruzada por path
- Verificación de replicación inter-directorio.
- Análisis de concentración por contenedor.

Esto permite validar que la simulación produce correctamente los escenarios Tipo A, B y C definidos previamente.

---

## 8.4 Validaciones temporales

Se verifica:

- Secuencia continua de días (sin saltos en T).
- Correspondencia entre `events_daily.day` y agregación de `blob_inventory.created_at`.
- Coherencia entre incident_flag y volumen observado.

Prueba clave:

\[
E[X_t | I_t=1] > E[X_t | I_t=0]
\]

Si esta condición no se cumple, la simulación estaría mal configurada.

---

## 8.5 Validaciones estadísticas

Se contrasta empíricamente:

1. Media vs varianza en días normales:
   \[
   Var(X_t) \approx E[X_t]
   \]

2. Media vs varianza en días con falla.

3. Tasa promedio observada:
   \[
   \bar{duplicate\_rate} \approx p_{ok}
   \text{ (en días normales)}
   \]

4. Tasa promedio observada:
   \[
   \bar{duplicate\_rate} \approx p_{fail}
   \text{ (en días con incidente)}
   \]

Estas verificaciones confirman coherencia entre parámetros teóricos y datos generados.

---

## 8.6 Validación de coherencia volumen–TPS

Se valida que:

\[
TPS_t = \frac{X_t}{86400}
\]

y que durante incidentes:

\[
E[TPS_t | I_t=1] > E[TPS_t | I_t=0]
\]

Esto garantiza consistencia entre modelo de conteo y métrica derivada.

---

## 8.7 Escalabilidad y validación en contexto Big Data

Para escenarios de mayor escala (GB–TB):

- Validar que la agregación diaria pueda ejecutarse en procesamiento distribuido.
- Validar particionamiento por fecha.
- Validar que el cálculo de hash no introduzca sesgo sistemático.
- Validar consistencia en ejecución paralela.

---

## 8.8 Conclusión sobre calidad del dataset

Las validaciones implementadas aseguran:

- Integridad estructural.
- Consistencia probabilística.
- Reproducibilidad experimental.
- Coherencia entre modelo teórico y datos simulados.

La calidad del dataset es condición necesaria para que los modelos de conteo y duplicación produzcan inferencias válidas.
