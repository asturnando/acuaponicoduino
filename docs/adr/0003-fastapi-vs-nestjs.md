# ADR-0003: FastAPI (Python 3.12) en lugar de NestJS, Go o Rust

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

El backend tiene que hacer:
- API REST con OpenAPI auto-generado.
- WebSocket para telemetría en vivo.
- Bridge MQTT (subscribe → ingest → DB).
- Workers async para notificaciones, agregaciones, anomalías.
- Integración cómoda con la API de Claude (tool use, prompt caching).

Los requisitos no son extremos en throughput — pico estimado: ~100 devices × 1 Hz = 100 msg/s, ampliamente manejable por cualquier framework moderno. Los criterios reales son: velocidad de desarrollo, ecosistema de librerías para esto en concreto, integración con IA, mantenibilidad solo.

### Opciones evaluadas

| Framework | Pros | Contras |
|-----------|------|---------|
| **FastAPI (Python)** | OpenAPI auto, Pydantic v2, async nativo, ecosistema ML/IA enorme, SDK Anthropic oficial, Arq como worker simple | GIL, runtime más pesado que Go/Rust |
| NestJS (TypeScript) | Estructura opinada, DI built-in, comparte tipos con web/mobile (TS) | Boilerplate alto, ecosistema MQTT/IA en Node menos maduro |
| Go (chi / Echo + sqlc) | Rendimiento muy alto, single binary, concurrency simple | OpenAPI auto requiere herramientas externas, integración Claude más artesanal |
| Rust (axum + sqlx) | Memory safety, rendimiento top | Velocidad de desarrollo ~½ comparado con Python a esta escala, ecosistema de IA aún incipiente en server-side |
| Django + DRF | Admin gratis, ORM maduro | Async retrofit, OpenAPI no first-class, peso histórico que no necesitamos |

## Decision

**Usamos FastAPI sobre Python 3.12+** con:
- **SQLAlchemy 2.0** + Alembic para ORM y migraciones.
- **Pydantic v2** para validación / serialización.
- **Arq** como worker async sobre Redis (más liviano que Celery a esta escala).
- **uv** como gestor de dependencias (reemplaza Poetry — order of magnitude más rápido).
- **structlog** para logs estructurados JSON.

## Consequences

### Positivas
- Velocidad de desarrollo: una persona sola puede mantener este backend.
- OpenAPI 3.1 auto-generado → cliente TypeScript tipado para web/mobile gratis.
- Integración trivial con Claude API (`anthropic` SDK oficial maduro en Python).
- Pydantic v2 da validación + serialización + docs en un solo modelo.
- Arq + Redis: 100 líneas de configuración total.

### Negativas
- Throughput por core inferior a Go o Rust (irrelevante a esta escala).
- GIL: workers CPU-bound necesitan procesos separados (irrelevante para nuestro IO-bound).
- Cold start ~1s por proceso (uvicorn) — irrelevante para servicios long-running.

### Neutras
- Tooling: ruff (lint + format) + mypy strict. Coverage objetivo > 70%.

## Referencias

- [FastAPI docs](https://fastapi.tiangolo.com/)
- [SQLAlchemy 2.0 ORM](https://docs.sqlalchemy.org/en/20/orm/)
- [Arq](https://arq-docs.helpmanual.io/)
- [Anthropic Python SDK](https://github.com/anthropics/anthropic-sdk-python)
- ADR-0004 (PostgreSQL + TimescaleDB)
