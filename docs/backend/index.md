# Backend

API REST + WebSocket + bridge MQTT + workers Arq sobre FastAPI.

!!! info "Estado"
    Vacío. Se rellena en **M3** del [Roadmap](../ROADMAP.md).

## Contenido previsto

- Setup local con `uv` y `docker compose`
- Migraciones Alembic e hypertables TimescaleDB
- Auth: JWT con refresh rotation + Argon2id
- Multi-tenancy y RBAC (`org_admin`, `operator`, `viewer`)
- API REST: organizations, users, devices, telemetry, alerts, calibration, OTA
- WebSocket `/devices/{id}/live`
- Bridge MQTT: ingestion en TimescaleDB, evaluación de reglas de alerta
- Workers Arq: notificaciones (push/email/Telegram), agregaciones, anomalías
- Audit log
- Métricas Prometheus en `/metrics`
- Rate limiting y headers de seguridad

Esquema de BD en [`ARCHITECTURE §6`](../ARCHITECTURE.md#6-esquema-de-base-de-datos-overview).
