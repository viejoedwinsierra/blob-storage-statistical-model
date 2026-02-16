# blob-storage-statistical-model

Marco metodológico reproducible para evaluar el estado de salud de almacenamiento en sistemas distribuidos, mediante detección de duplicados por contenido, modelamiento estadístico de conteos y análisis temporal de picos inducidos por fallas.

---

## 📁 Entregable principal

Haz clic en cada sección:

1. [01 - Contexto del problema](docs/01_contexto_problema.md)
2. [02 - Procedencia y fuente](docs/02_procedencia_fuente.md)
3. [03 - Estructura del dataset](docs/03_estructura_dataset.md)
4. [04 - Parámetros de simulación](docs/04_parametros_simulacion.md)
5. [05 - Modelamiento estadístico](docs/05_modelamiento_estadistico.md)
6. [06 - Preguntas analíticas](docs/06_preguntas_analiticas.md)
7. [07 - Limitaciones metodológicas](docs/07_limitaciones_metodologia.md)
8. [08 - Validaciones de calidad](docs/08_validaciones_calidad.md)
9. [09 - Modelo causal de fallas](docs/09_modelo_causal_fallas.md)
10. [10 - Referencias](docs/10_referencias.md)

---

## 📊 Parámetros del modelo

- N = 10,000 contenidos únicos  
- 180 días de simulación  
- λ = 1000 blobs/día  
- k = 3 (factor de spike en fallas)  
- p_ok = 0.02  
- p_fail = 0.10  

---

Proyecto desarrollado para la asignatura **Modelos Estadísticos**.
