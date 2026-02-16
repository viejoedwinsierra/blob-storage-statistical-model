---
🏠 [Inicio](../README.md)

⬅️ [Anterior](02_procedencia_fuente.md)

➡️ [Siguiente](04_parametros_simulacion.md)

---


# 3. Descripción estructural de la base de datos

Horizonte temporal: 180 días  
Total esperado: ~198,000 blobs  

## blob_inventory
Variables:
- blob_id
- container
- path
- blob_name
- size_bytes
- content_hash
- created_at

## events_daily
- day
- X_t (blobs creados)
- D_t (duplicados)
- duplicate_rate
- incident_flag
- TPS_t
