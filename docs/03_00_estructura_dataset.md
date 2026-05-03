---
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_00_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---

## 3.4 Definición de variables del dataset

📌 Este dataset constituye la base del modelo estadístico.

Para la definición detallada del modelo:

➡️ [Variables independientes (X)](03_01_variables_independientes.md)  
➡️ [Variables dependientes (Y)](03_02_variables_dependientes.md)

---

### Tabla: Definición completa de variables

| Variable | Tipo de dato | Tipo de variable | Clasificación | Rol | Descripción |
|----------|-------------|------------------|--------------|-----|-------------|
| file_id | Texto | Identificador | - | Técnica | Identificador único del archivo |
| file_type | Categórica | Nominal | X | Explicativa | Tipo de archivo (json, jpg, pdf, mp4) |
| size_gb | Numérica | Cuantitativa continua | X | Clave | Tamaño del archivo en GB |
| storage_tier | Categórica | Nominal | X | Clave | Nivel de almacenamiento (hot, cool, archive) |
| days_stored | Numérica | Cuantitativa discreta | X | Clave | Tiempo de almacenamiento en días |
| days_since_last_access | Numérica | Cuantitativa discreta | X | Explicativa | Tiempo desde último acceso |
| read_level | Categórica | Ordinal | X | Explicativa | Nivel de lectura del archivo |
| modify_level | Categórica | Ordinal | X | Explicativa | Nivel de modificación |
| movement_storage | Numérica (0/1) | Binaria | X | Explicativa | Cambio de tier |
| transfer_duration_sec | Numérica | Cuantitativa continua | X | Explicativa | Duración de transferencia |
| transfer_speed_mbps | Numérica | Cuantitativa continua | X | Derivada ⚠️ | Variable dependiente de tamaño y tiempo |
| hash_head | Texto | Cualitativa nominal | X | Explicativa ⚠️ | Segmento inicial del contenido |
| hash_tail | Texto | Cualitativa nominal | X | Explicativa ⚠️ | Segmento final del contenido |
| error_duplicado | Numérica (0/1) | Binaria | X | Secundaria | Duplicidad lógica |
| error_orphan | Numérica (0/1) | Binaria | X | Secundaria | Inconsistencia |
| error_null | Numérica (0/1) | Binaria | X | Secundaria | Registro vacío |
| error_blob_timeout | Numérica (0/1) | Binaria | X | Secundaria | Error operativo |
| storage_cost | Numérica | Cuantitativa continua | Y | Dependiente | Costo de almacenamiento |

---

### Clasificación formal del modelo

#### Variable dependiente

$$
Y = storage_{cost}
$$

---

#### Variables independientes

$$
X = \{
size_{gb},
file_{type},
storage_{tier},
days_{stored},
error_i,
hash_{head},
hash_{tail}
\}
$$

---

### Relación del modelo

$$
Y = f(X)
$$

$$
P(Y \mid X)
$$

---

### Observaciones metodológicas

- Eliminación de redundancia (MB vs GB)
- Dependencia estructural:

$$
storage_{cost} =
size_{gb} \cdot tarifa_{tier} \cdot \frac{days_{stored}}{30}
$$

- Variable derivada:

$$
transfer_{speed} = \frac{size}{tiempo}
$$

- Alta cardinalidad ⚠️ en hash
- Variables de error binarias

---

### 🔗 Navegación interna del modelo

➡️ [Ir a Variables independientes](03_01_variables_independientes.md)  
➡️ [Ir a Variables dependientes](03_02_variables_dependientes.md)  

---
