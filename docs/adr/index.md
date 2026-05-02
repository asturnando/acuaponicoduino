# Architecture Decision Records (ADRs)

Las ADR documentan **decisiones arquitecturales relevantes**: el contexto en el que se tomaron, la decisión, y las consecuencias asumidas.

> Si en el futuro alguien (incluido tú dentro de 6 meses) se pregunta _"¿por qué hicimos X así?"_, la respuesta debe estar aquí — no en la cabeza de nadie.

## Formato

Seguimos el formato de [Michael Nygard](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions): **Status / Context / Decision / Consequences**. Plantilla en [`template.md`](template.md).

## Cuándo escribir una ADR

- Antes de implementar un cambio que afecte a más de un componente.
- Cuando se cambie una decisión previa (la nueva ADR _supersedes_ la anterior, no se borra).
- Cuando una opción "obvia" se descarta por una razón no obvia.

## Cuándo NO

- Cambios triviales de implementación.
- Refactors internos sin impacto cross-component.
- Decisiones reversibles en < 1 día.

## Ciclo de vida

| Status | Significado |
|--------|-------------|
| `Proposed` | En discusión |
| `Accepted` | Aprobada y vigente |
| `Deprecated` | Vigente pero a sustituir |
| `Superseded by ADR-NNNN` | Reemplazada por otra ADR |

## Índice

| # | Título | Status |
|---|--------|--------|
| [0001](0001-esp32s3-vs-mega-esp8266.md) | ESP32-S3 vs Mega+ESP8266 | Accepted |
| [0002](0002-esp-idf-vs-arduino.md) | ESP-IDF vs Arduino framework | Accepted |
| [0003](0003-fastapi-vs-nestjs.md) | FastAPI vs NestJS para el backend | Accepted |
| [0004](0004-postgres-timescale-vs-influx-mongo.md) | PostgreSQL + TimescaleDB vs InfluxDB vs Mongo | Accepted |
| [0005](0005-nextjs-vs-remix-vs-sveltekit.md) | Next.js vs Remix vs SvelteKit para web | Accepted |
| [0006](0006-react-native-expo-vs-flutter.md) | React Native + Expo vs Flutter para móvil | Accepted |
| [0007](0007-mosquitto-vs-emqx-vs-hivemq.md) | Mosquitto vs EMQX vs HiveMQ para broker MQTT | Accepted |
| [0008](0008-monorepo-vs-polyrepo.md) | Monorepo vs poly-repo | Accepted |
| [0009](0009-licencia-agplv3.md) | Licencia AGPLv3 | Accepted |
| [0010](0010-hosting-hetzner.md) | Hosting en Hetzner Cloud | Accepted |
