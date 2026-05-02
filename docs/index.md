---
hide:
  - navigation
---

# AcuaponicDuino v2.0

Sistema **commercial-grade** de control y monitorización para acuaponía doméstica y semi-profesional. Refactor del [TFG original de 2022](https://github.com/asturnando/AcuaponicDuino-master) (Fernando Suárez Rodríguez, UOC).

!!! warning "Estado"
    Proyecto en **diseño**. Sin código ni hardware fabricado todavía. La arquitectura y el roadmap son los dos documentos de referencia.

## ¿Qué es esto?

Una unidad embebida (ESP32-S3 sobre PCB custom 4 capas) controla todos los sensores y actuadores de un sistema acuapónico. Backend multi-tenant en la nube, web y app móvil, OTA seguro, asistente IA. Local-first: el sistema funciona sin internet.

## Empieza por aquí

<div class="grid cards" markdown>

-   :material-file-document-multiple: __[Arquitectura](ARCHITECTURE.md)__

    ---

    Decisiones cerradas, pinout, topics MQTT, esquema BD, flujos. Si esto cambia, todo se ajusta.

-   :material-checkbox-marked-circle-outline: __[Roadmap](ROADMAP.md)__

    ---

    Checklist exhaustiva por milestones. La fuente de verdad del progreso.

-   :material-github: __[Onboarding GitHub](ONBOARDING_GITHUB.md)__

    ---

    Pasos manuales para configurar el repo remoto la primera vez.

-   :material-clipboard-text-outline: __[ADRs](adr/index.md)__

    ---

    Decisiones arquitecturales escritas con su contexto y consecuencias.

</div>

## Stack en una página

| Capa | Tecnología |
|------|-----------|
| MCU | ESP32-S3-WROOM-1 N8R2 |
| Firmware | ESP-IDF v5.3+ + PlatformIO |
| PCB | KiCad 8, 4 capas |
| Broker | Mosquitto 2 (TLS, mTLS) |
| BD | PostgreSQL 16 + TimescaleDB |
| Backend | FastAPI + Arq |
| Web | Next.js 15 + Tailwind v4 |
| Mobile | React Native + Expo |
| IA | Claude API (`claude-sonnet-4-6`) |
| Hosting | Hetzner Cloud |
| Licencia | [AGPLv3](https://github.com/asturnando/acuaponicoduino/blob/main/LICENSE) |

## Filosofía

- **Hard comercial + Soft comercial** con BOM hobby (sin sondas Atlas Scientific)
- **Portfolio-grade hoy**, semilla de producto comercial mañana
- **Local-first**: el sistema funciona sin internet. El cloud es opcional.
- **Versionable**: todo en git, incluyendo PCB, dashboards y configuraciones
- **Sin atajos** que comprometan auditoría futura (CE, GDPR, HACCP)
