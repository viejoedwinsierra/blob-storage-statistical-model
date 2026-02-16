# 3. Descripción estructural de la base de datos

Tablas principales:

## blob_inventory
- blob_id
- container
- path
- blob_name
- size_bytes
- content_hash
- created_at

## events_daily
- day
- blobs_created_count (X_t)
- duplicate_count (D_t)
- duplicate_rate
- incident_flag
- TPS_t
