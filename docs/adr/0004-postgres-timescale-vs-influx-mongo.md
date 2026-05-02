# ADR-0004: PostgreSQL 16 + TimescaleDB en una sola BD

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

Datos a persistir:
- **Relacionales**: organizations, users, devices, calibrations, alert_rules, audit_log, firmware_releases. Volumen bajo (< 10⁵ filas), pero requieren joins, transacciones, integridad referencial.
- **Series temporales**: telemetría de sensores. Volumen alto (~100 devices × 6 sensores × 1 Hz = 600 inserts/s pico, ~52 M filas/día worst-case). Necesita: agregaciones por rangos, retention policies, downsampling automático.

### Opciones evaluadas

| Stack | Pros | Contras |
|-------|------|---------|
| **PostgreSQL 16 + TimescaleDB** | Una sola BD, una sola conexión, una sola backup-strategy. SQL estándar para todo. Hypertables, continuous aggregates, retention nativos. Joins entre relacional y series sin overhead | TimescaleDB Apache 2.0 license tiene features Enterprise solo en versión propietaria (no las necesitamos a esta escala) |
| Postgres + InfluxDB | InfluxDB optimizado para series, Flux es potente | Dos BD, dos conexiones, dos backups, sin joins cross-DB, más infra |
| Postgres + ClickHouse | Throughput brutal | Overkill a esta escala; otro engine que mantener |
| Postgres puro (sin extension) | Cero infra extra | Sin downsampling automático, sin retention, queries de ventana ineficientes a 50M+ filas |
| MongoDB | Schema flexible | Sin transacciones cross-collection robustas, queries de series temporales más artesanales |

## Decision

**Usamos PostgreSQL 16 + TimescaleDB** como única base de datos. Una conexión, un orquestador.

- Tablas relacionales: Postgres puro.
- `telemetry`: hypertable particionada por día (`time` + `device_id`).
- Continuous aggregates: `telemetry_5m`, `telemetry_1h`, `telemetry_1d`.
- Retention policies:
    - Raw: 90 días
    - 5min: 6 meses
    - 1h: 2 años
    - 1d: 5 años
- Backups: `pg_dump` cifrado con `age` → Hetzner Object Storage, retención 30d.

Esquema completo en [`ARCHITECTURE §6`](../ARCHITECTURE.md#6-esquema-de-base-de-datos-overview).

## Consequences

### Positivas
- SQL para todo, una sola fuente de verdad.
- Joins entre `telemetry` y `devices`/`alerts_history` sin servicios intermedios.
- Continuous aggregates eliminan la necesidad de un worker que pre-agregue manualmente.
- Backup unificado, restore unificado.
- Multi-tenancy con `org_id` filter en todas las queries (RLS opcional cuando crezca).

### Negativas
- TimescaleDB añade una imagen Docker custom (`timescale/timescaledb`) — un poco más de infra que Postgres vanilla.
- Algunos hosters managed (RDS, Cloud SQL) no soportan TimescaleDB — irrelevante porque self-hosteamos en Hetzner.
- License Apache 2.0 con features Enterprise propietarias: no nos afecta a esta escala, pero tener presente si crece.

### Neutras
- ORM: SQLAlchemy 2.0 funciona con TimescaleDB sin extras (los `create_hypertable` van en migraciones Alembic crudas).

### Riesgos
- Volumen sostenido > 10k inserts/s requeriría sharding o cambiar a ClickHouse. Lejos de nuestro target.

## Referencias

- [TimescaleDB docs](https://docs.timescale.com/)
- [Continuous aggregates](https://docs.timescale.com/use-timescale/latest/continuous-aggregates/)
- ADR-0003 (FastAPI + SQLAlchemy)
- [ARCHITECTURE.md §6](../ARCHITECTURE.md#6-esquema-de-base-de-datos-overview)
