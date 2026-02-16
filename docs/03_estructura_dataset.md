--
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---

# 3. Estructura y generación del dataset

## 3.1 Objetivo

Construir un dataset estructurado que represente el inventario de blobs generados por el sistema durante un horizonte de 180 días, incorporando tanto operación normal como eventos de falla que generan duplicación por contenido.

Este dataset servirá como base para:

- Modelado estadístico del crecimiento
- Estimación del impacto en almacenamiento
- Identificación de anomalías
- Simulación de escenarios futuros

---

## 3.2 Fuente primaria de datos

La fuente del dataset es el almacenamiento de objetos (Blob Storage), donde:

- Cada transacción genera un archivo (~1MB)
- Cada archivo posee nombre único
- En eventos de falla pueden generarse duplicados por contenido
- El duplicado mantiene mismo hash pero distinto nombre

---

## 3.3 Proceso de construcción del dataset

La generación del dataset sigue tres etapas:

### 3.3.1 Enumeración de blobs

Lectura completa del contenedor:

- Listado de blobs
- Extracción de metadatos:
  - Nombre
  - Ruta
  - Tamaño
  - Fecha de creación
  - Contenedor

Esto produce la tabla base `blob_inventory`.

---

## 3.4 Estructura del dataset

Horizonte temporal: 180 días  
Total esperado aproximado: ~198,000 blobs  

---

### 3.4.1 Tabla: blob_inventory

| Campo | Tipo | Descripción |
|-------|------|------------|
| blob_id | UUID | Identificador interno |
| container | string | Contenedor lógico |
| path | string | Prefijo o estructura jerárquica |
| blob_name | string | Nombre físico del archivo |
| size_bytes | integer | Tamaño del blob |
| content_hash | string | Hash SHA256 del contenido |
| created_at | timestamp | Fecha de creación |

#### Propósito

Permite:

- Medir crecimiento bruto
- Detectar duplicados por contenido
- Calcular almacenamiento acumulado
- Analizar distribución temporal

---

### 3.4.2 Tabla: events_daily

Agregación diaria derivada de `blob_inventory`.

| Campo | Descripción |
|-------|------------|
| day | Fecha calendario |
| X_t | Total blobs creados en el día |
| D_t | Número de blobs duplicados (hash repetido) |
| duplicate_rate | D_t / X_t |
| incident_flag | 1 si duplicate_rate supera umbral |
| TPS_t | Transacciones por segundo estimadas |

---

## 3.5 Detección de duplicación

La duplicación se define como:

Dos blobs son duplicados si:

- content_hash es idéntico
- blob_name es distinto

Formalmente:

D_t = número de blobs con frecuencia(hash) > 1

La tasa de duplicación:

duplicate_rate = D_t / X_t

---

## 3.6 Construcción en caso de dataset simulado

Si los datos no provienen de un entorno real, se genera el dataset bajo los siguientes supuestos:

- λ = media diaria de transacciones
- p_fail = probabilidad de día con falla
- k = factor multiplicativo durante falla

Modelo:

- Día normal:
  X_t ~ Poisson(λ)

- Día con falla:
  X_t = k × Poisson(λ)

Asignación de hashes:

- Día normal: hashes únicos
- Día con falla: reutilización parcial de hashes para simular reintentos

---

## 3.7 Validaciones estructurales

El dataset debe cumplir:

- size_bytes ≈ 1MB ± variación mínima
- duplicate_rate ≈ 0 en días normales
- duplicate_rate ≈ (k − 1)/k en días con falla
- Crecimiento acumulado consistente con modelo teórico

---

## 3.8 Relación con el modelo estadístico

El dataset permite estimar:

Volumen diario esperado:

E[V] = λ · S · [(1 − p_fail) + p_fail · k]

donde:

- S ≈ 1MB
- λ = tasa media
- p_fail = probabilidad de incidente
- k = factor multiplicativo

Este modelo conecta directamente la estructura física del almacenamiento con el análisis probabilístico del crecimiento.

---

