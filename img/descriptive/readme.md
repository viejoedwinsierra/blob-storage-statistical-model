# Salidas del análisis descriptivo

Este directorio contiene los gráficos descriptivos generados por `run_analysis.py`.

## Gráficos principales

- `size_mb_histogram.png`: distribución del tamaño de archivos.
- `size_mb_boxplot.png`: outliers de tamaño.
- `storage_cost_histogram.png`: distribución de costos.
- `storage_cost_boxplot.png`: outliers de costo.
- `correlation_matrix.png`: matriz de correlación numérica.
- `daily_records.png`: registros diarios con eje temporal formateado.
- `daily_error_rate.png`: tasa diaria de error con eje temporal formateado.
- `daily_total_cost.png`: costo total diario.
- `daily_avg_transfer_duration.png`: duración promedio diaria.
- `storage_cost_by_tier.png`: costos segmentados por storage tier.
- `transfer_duration_by_size_range.png`: duración segmentada por rango de tamaño.
- `error_rate_heatmap_date_hour.png`: heatmap de tasa de error por fecha y hora.

## Tablas HTML

Las tablas HTML se generan en:

`output/descriptive_exports`

Incluyen análisis descriptivo general, nulos, outliers, correlaciones, resumen diario, resumen por storage tier y resumen por rango de tamaño.