---
🏠 [Inicio](../README.md)

⬅️ [Anterior](03_estructura_dataset.md)

➡️ [Siguiente](05_modelamiento_estadistico.md)

---

# 4. Parámetros de simulación

## 4.1 Propósito de la simulación

La simulación permite generar un dataset sintético a nivel de archivo para estudiar el comportamiento de un sistema de almacenamiento de objetos.

El modelo se centra en:

1. Variabilidad del tipo de archivo: `json`, `jpg`, `pdf`, `mp4`.
2. Tamaño del archivo.
3. Ciclo de vida del dato.
4. Nivel de almacenamiento.
5. Errores binarios.
6. Persistencia aproximada del contenido mediante `hash_head` y `hash_tail`.

El resultado principal de la simulación es la tabla:

- `blob_inventory` — nivel archivo/blob.

---

## 4.2 Horizonte y tamaño del experimento

### 4.2.1 Horizonte temporal

- **T = 180 días**

Este horizonte permite observar variabilidad temporal, permanencia del dato y comportamiento del almacenamiento durante un período equivalente a seis meses.

### 4.2.2 Tamaño muestral

- **N = 10,000 archivos**

Cada fila del dataset representa un archivo simulado.

Formalmente:

$$
\mathcal{D} = \{u_i\}_{i=1}^{N}
$$

donde cada instancia \(u_i\) corresponde a un archivo almacenado.

---

## 4.3 Parámetros de tipos de archivo

El tipo de archivo se modela como una variable categórica:

$$
file_{type} \sim \text{Categorical}(p_{json}, p_{jpg}, p_{pdf}, p_{mp4})
$$

Valores permitidos:

| Tipo | Uso esperado |
|---|---|
| json | Metadatos |
| jpg | Imagen |
| pdf | Documento |
| mp4 | Multimedia |

Esta decisión controla la variedad del dataset y permite comparar comportamientos diferenciados por tipo de archivo.

---

## 4.4 Parámetros de tamaño

El tamaño del archivo se expresa únicamente en GB para evitar redundancia entre unidades.

$$
size_{gb} \sim F_s
$$

Se recomienda usar distribuciones diferenciadas por tipo de archivo:

| Tipo | Rango sugerido |
|---|---:|
| json | 0.000001 GB – 0.0005 GB |
| jpg | 0.0001 GB – 0.008 GB |
| pdf | 0.00005 GB – 0.02 GB |
| mp4 | 0.005 GB – 0.5 GB |

El uso de una distribución log-normal permite representar colas largas:

$$
size_{gb} \sim \text{LogNormal}(\mu_{type}, \sigma_{type})
$$

---

## 4.5 Parámetros de ciclo de vida

El tiempo de almacenamiento se modela como:

$$
days_{stored} \sim F_t
$$

Para mantener control del experimento, se puede usar:

$$
days_{stored} \sim \text{Uniform}(1, 180)
$$

El tiempo desde el último acceso se modela condicionado al tiempo almacenado:

$$
days_{since\_last\_access} \sim \text{Uniform}(0, days_{stored})
$$

Esto permite clasificar archivos activos, fríos o candidatos a movimiento de nivel.

---

## 4.6 Parámetros de nivel de almacenamiento

El nivel de almacenamiento se modela como variable categórica:

$$
storage_{tier} \sim \text{Categorical}(p_{hot}, p_{cool}, p_{archive})
$$

Valores permitidos:

| Tier | Interpretación |
|---|---|
| hot | Alta lectura |
| cool | Acceso medio |
| archive | Baja lectura |

---

## 4.7 Parámetros operativos de transferencia

La duración de transferencia se modela como:

$$
transfer_{duration\_sec} \sim F_d
$$

La velocidad de transferencia se calcula como variable derivada:

$$
transfer_{speed\_mbps} =
\frac{size_{gb} \cdot 1024 \cdot 8}{transfer_{duration\_sec}}
$$

⚠️ Esta variable depende del tamaño y del tiempo de transferencia, por lo que debe evaluarse con cuidado para evitar redundancia o multicolinealidad.

---

## 4.8 Parámetros de lectura, modificación y movimiento

Los niveles de lectura y modificación se modelan como variables ordinales:

$$
read_{level} \sim \text{Categorical}(low, medium, high)
$$

$$
modify_{level} \sim \text{Categorical}(low, medium, high)
$$

El movimiento entre niveles de almacenamiento se representa como variable binaria:

$$
movement_{storage} \sim \text{Bernoulli}(p_{move})
$$

---

## 4.9 Parámetros de error

Los errores se modelan como variables binarias independientes:

$$
error_i \sim \text{Bernoulli}(p_i)
$$

Variables consideradas:

| Variable | Significado |
|---|---|
| error_duplicado | Duplicidad lógica |
| error_orphan | Archivo o metadata huérfana |
| error_null | Registro sin contenido |
| error_blob_timeout | Error operativo de transferencia |

El indicador general de error puede definirse como:

$$
has_{error} =
\mathbb{1}(error_{duplicado} + error_{orphan} + error_{null} + error_{blob\_timeout} > 0)
$$

---

## 4.10 Parámetros de hash parcial

No se calcula ni analiza el hash completo del archivo por costo computacional.

Se utilizan segmentos parciales:

$$
hash_{head} = H(content)[0:k]
$$

$$
hash_{tail} = H(content)[-k:]
$$

Estas variables funcionan como:

- proxy probabilístico de persistencia
- indicador de recurrencia del contenido
- apoyo para detección temprana de duplicados

⚠️ No constituyen evidencia absoluta de duplicidad.

---

## 4.11 Parámetros de costo

Aunque el costo no es una variable independiente, puede calcularse como variable resultado del sistema.

$$
storage_{cost} =
size_{gb} \cdot rate_{tier} \cdot \frac{days_{stored}}{30}
$$

donde:

- \(size_{gb}\): tamaño del archivo
- \(rate_{tier}\): tarifa del nivel de almacenamiento
- \(days_{stored}\): tiempo almacenado

---

## 4.12 Relación probabilística del modelo

El conjunto de variables independientes se define como:

$$
X = \{size_{gb}, file_{type}, storage_{tier}, days_{stored}, read_{level}, modify_{level}, error_i, hash_{head}, hash_{tail}\}
$$

Las variables resultado pueden expresarse como:

$$
Y = \{storage_{cost}, has_{error}, is_{duplicate}\}
$$

El objetivo general del análisis es estudiar:

$$
P(Y \mid X)
$$

---

## 4.13 Resumen de parámetros

| Parámetro | Valor sugerido | Tipo | Significado |
|---|---:|---|---|
| T | 180 | entero | Horizonte temporal |
| N | 10,000 | entero | Número de archivos |
| file_type | json, jpg, pdf, mp4 | categórico | Tipo de archivo |
| size_gb | según tipo | continua | Tamaño del archivo |
| storage_tier | hot, cool, archive | categórico | Nivel de almacenamiento |
| days_stored | 1–180 | discreta | Tiempo almacenado |
| days_since_last_access | 0–days_stored | discreta | Último acceso |
| read_level | low, medium, high | ordinal | Nivel de lectura |
| modify_level | low, medium, high | ordinal | Nivel de modificación |
| movement_storage | 0/1 | binaria | Cambio de tier |
| error_* | 0/1 | binaria | Condiciones de error |
| hash_head | texto | nominal ⚠️ | Segmento inicial |
| hash_tail | texto | nominal ⚠️ | Segmento final |
| storage_cost | calculado | continua | Resultado del sistema |

---

## 4.14 Escenarios de simulación recomendados

Para análisis comparativo se sugieren:

1. **Baseline**: baja tasa de errores y distribución balanceada de tipos.
2. **Alta presencia multimedia**: mayor proporción de archivos mp4.
3. **Alta duplicidad lógica**: aumento de `error_duplicado`.
4. **Almacenamiento frío**: mayor proporción en `archive`.
5. **Alta actividad**: mayor lectura y modificación.

Estos escenarios permiten estudiar sensibilidad sobre volumen, costo, calidad y ciclo de vida.

---

## 4.15 Notas de consistencia

- No usar simultáneamente `size_mb` y `size_gb`.
- No incluir `storage_cost` como variable independiente si se usa como resultado.
- Tratar `hash_head` y `hash_tail` como variables auxiliares de alta cardinalidad ⚠️.
- Diferenciar claramente entre variables observables, variables derivadas y variables objetivo.
- Mantener ecuaciones en formato de bloque con `$$`.

---
