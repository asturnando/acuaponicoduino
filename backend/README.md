# backend/

API REST + WebSocket + bridge MQTT + workers async. Python 3.12+ sobre FastAPI.

## Estado

Vacío. Se llenará a partir de **M3** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
backend/
├── pyproject.toml          # uv o Poetry
├── alembic.ini
├── app/
│   ├── api/                # endpoints FastAPI por recurso
│   ├── models/             # SQLAlchemy 2.0
│   ├── schemas/            # Pydantic v2
│   ├── services/           # lógica de negocio
│   ├── workers/            # tareas Arq
│   ├── core/               # config, security, deps
│   └── mqtt/               # bridge: subscribe → ingest → DB
├── migrations/             # Alembic
├── tests/                  # pytest
└── .env.example
```

## Stack

| Pieza         | Tecnología                                    |
| ------------- | --------------------------------------------- |
| Framework     | FastAPI                                       |
| ORM           | SQLAlchemy 2.0                                |
| Migraciones   | Alembic                                       |
| Validación    | Pydantic v2                                   |
| DB            | PostgreSQL 16 + TimescaleDB                   |
| Cache / queue | Redis 7                                       |
| Workers       | Arq                                           |
| Auth          | JWT (15min access + 30d refresh con rotation) |
| Passwords     | Argon2id                                      |
| Broker        | Mosquitto 2 (TLS, mTLS para devices)          |

## Run local

```bash
cd backend
uv sync                                     # o poetry install
docker compose -f ../infra/docker-compose.dev.yml up -d postgres redis mosquitto
uv run alembic upgrade head
uv run uvicorn app.main:app --reload
```

OpenAPI en `http://localhost:8000/docs`.

## Tests

```bash
uv run pytest --cov=app
```

Coverage objetivo: > 70%.
