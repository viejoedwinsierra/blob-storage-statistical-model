---
🏠 [Inicio](../README.md)

⬅️ [Anterior](07_limitaciones_metodologia.md)

➡️ [Siguiente](09_modelo_causal_fallas.md)

---

# 8. Validaciones de calidad

La calidad del dataset es fundamental para garantizar que el modelamiento estadístico refleje correctamente el comportamiento simulado del sistema.

Las validaciones se organizan en cuatro niveles:

---

## 8.1 Validaciones estructurales

Se verifica:

- `size_bytes >= 0`
- `content_hash` no nulo
- unicidad de `blob_id`
- coherencia entre `created_at` y `last_modified_at`
- consistencia de la estructura jerárquica del `path`

Estas validaciones aseguran integridad básica del dataset.

---

## 8.2 Validaciones de duplicación

Se comprueba que:

- Los grupos por `content_hash` reflejen correctamente la duplicación simulada.
- En días con incidente, la proporción de hashes repetidos sea mayor.
- No existan duplicados inconsistentes fuera del modelo definido.

Esto garantiza coherencia con los parámetros \(p_{ok}\) y \(p_{fail}\).

---

## 8.3 Validaciones temporales

Se valida que:

- El número de días sea T = 180.
- La agregación diaria coincida con la suma de registros en `blob_inventory`.
- En promedio:
  \[
  E[X_t | I_t=1] > E[X_t | I_t=0]
  \]

Esto confirma que el efecto del factor \(k\) está correctamente implementado.

---

## 8.4 Validaciones estadísticas básicas

Se contrastan empíricamente:

- Media aproximada de \(X_t\) cercana a λ en días normales.
- Media aproximada de \(X_t\) cercana a kλ en días con incidente.
- Tasa promedio de duplicación cercana a \(p_{ok}\) y \(p_{fail}\) según el estado.

Estas comprobaciones permiten verificar que la simulación respeta el modelo teórico.

---

## 8.5 Consideración de escalabilidad

El modelo está diseñado para escalar a volúmenes mayores, trabajando sobre agregaciones y metadatos en lugar de inspección completa del contenido.

Esto es coherente con prácticas de análisis en entornos Big Data.
