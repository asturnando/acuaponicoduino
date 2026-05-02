# AcuaponicDuino v2.0

Refactor commercial-grade del TFG de 2022 — control y monitorización de cultivos acuapónicos.

> **Estado:** en diseño. Sin código aún. La arquitectura y el roadmap son los dos documentos de referencia.

## Origen

Este proyecto evoluciona el [TFG original de 2022](../AcuaponicDuino-master/) (Fernando Suárez Rodríguez, UOC). El TFG conceptualmente era correcto pero arrastraba bugs de FreeRTOS, hardware en protoboard, sin OTA, sin cloud, sin app y sin tests. La v2 reescribe todo manteniendo:

- La selección de **sensores** (Gravity pH/TDS, DS18B20, FS400A, FS-IR02, etc.)
- La **arquitectura conceptual** de tareas y topics MQTT
- El **caso de uso** (acuaponía doméstica/semi-pro)

Y reemplaza:

- Mega 2560 + ESP8266 (dual board) → **ESP32-S3 único**
- Protoboard → **PCB custom 4 capas** en KiCad
- Protocolo serial custom → **JSON sobre MQTT 5.0 con TLS**
- Sin cloud → **backend multi-tenant** (FastAPI + TimescaleDB)
- Sin app → **web (Next.js)** + **móvil (React Native)**
- Sin OTA → **OTA dual-partition con rollback**
- Sin IA → **asistente Claude** sobre datos del cliente

## Documentos clave

| Doc | Propósito |
|-----|-----------|
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | Decisiones cerradas, pinout, topics, BD, flujos. Si esto cambia, todo lo demás se ajusta. |
| [`docs/ROADMAP.md`](docs/ROADMAP.md) | Checklist exhaustiva de todas las tareas, agrupadas por fase. La fuente de verdad del progreso. |

## Filosofía

- **Hard comercial + Soft comercial** con BOM hobby (sin sondas Atlas Scientific)
- **Portfolio-grade hoy**, semilla de producto comercial mañana
- **Local-first**: el sistema funciona sin internet. El cloud es opcional.
- **Versionable**: todo en git, incluyendo PCB, dashboards y configuraciones
- **Sin atajos** que comprometan auditoría futura (CE, GDPR, HACCP)

## Estructura prevista del monorepo

```
acuaponicoduino/
├── firmware/      # ESP-IDF + PlatformIO
├── hardware/      # KiCad + carcasa
├── backend/       # FastAPI
├── infra/         # Docker compose + IaC
├── web/           # Next.js
├── mobile/        # React Native + Expo
├── ai/            # Asistente Claude
└── docs/          # mkdocs-material
```

## Licencia

**[GNU AGPLv3](LICENSE)** — preserva la opción de dual-licensing comercial en el futuro. Si en algún momento decides relicenciar a MIT u otra más permisiva, al ser único contribuidor es un cambio trivial. Al revés no se puede.

## Documentos del repositorio

- [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) · [`docs/ROADMAP.md`](docs/ROADMAP.md) · [`docs/adr/`](docs/adr/)
- [`CONTRIBUTING.md`](CONTRIBUTING.md) · [`SECURITY.md`](SECURITY.md) · [`CODE_OF_CONDUCT.md`](CODE_OF_CONDUCT.md) · [`CHANGELOG.md`](CHANGELOG.md)
