# ADR-0010: Hosting en Hetzner Cloud (CX22 inicial)

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

Necesitamos hosting para:
- API backend (FastAPI + Arq workers).
- PostgreSQL + TimescaleDB.
- Redis.
- Mosquitto (MQTT broker, puerto 8883).
- Caddy (TLS automático).
- Stack monitoring (Prometheus, Grafana, Loki).
- Web Next.js.
- Documentación mkdocs.

Estimación inicial (~10 devices piloto): RAM total < 2 GB, CPU peak < 1 vCPU, storage ~20 GB.

### Opciones evaluadas

| Provider | Pros | Contras |
|----------|------|---------|
| **Hetzner Cloud (CX22, ~5€/mes)** | Precio imbatible (4 GB RAM, 2 vCPU, 40 GB SSD por 5€), datacenter Falkenstein/Helsinki bajo GDPR, red rápida, panel sencillo | EU-only (irrelevante para nosotros), ecosistema más pequeño que AWS |
| OVH VPS | Similar precio | Soporte y panel históricamente más torpes que Hetzner |
| DigitalOcean | DX excelente, marketplaces | ~2× precio para mismo recurso |
| AWS (EC2 t4g.small) | Servicios managed disponibles | Coste fácilmente 3-5× para piloto, complejidad innecesaria |
| Google Cloud / Azure | Idem AWS | Idem |
| Railway / Render / Fly.io | Deploy con `git push` | Coste compuesto sube rápido con TimescaleDB + workers + WS |
| Self-hosted en casa | Coste cero | IP dinámica, riesgo de uptime, ruido eléctrico, contradice "demo profesional" |

## Decision

**VPS Hetzner Cloud, modelo CX22 (~5€/mes, Falkenstein)** como infraestructura inicial.

- **OS**: Debian 12 minimal.
- **Hardening**: SSH key-only (no password), UFW + Hetzner firewall (22 SSH, 80/443, 8883), fail2ban, unattended-upgrades.
- **Container runtime**: Docker + docker compose.
- **Reverse proxy**: Caddy con HTTPS automático (Let's Encrypt) para `api.*`, `app.*`, `docs.*`.
- **Backups**: `pg_dump` cifrado con `age` → Hetzner Object Storage, retención 30d.
- **Monitoring**: Prometheus + node_exporter + Grafana + Loki en el mismo nodo (todo en compose).
- **CI/CD**: GitHub Actions hace SSH al nodo y corre `docker compose pull && up -d` (con healthchecks).
- **Setup reproducible**: script bash idempotente en `infra/scripts/setup-hetzner.sh`. **Terraform opcional** para v3.

## Consequences

### Positivas
- 5€/mes en piloto, escalable a CX32 (~10€) o CX42 (~20€) sin migración (cambio de plan en panel).
- GDPR-friendly por defecto (datacenter EU).
- Una sola caja → debugging y backup sencillos.
- Hetzner Object Storage S3-compatible para backups (~0.005€/GB/mes).

### Negativas
- Single point of failure: si la VPS muere, el cloud cae. Mitigación: backups diarios + script de restore probado. Para HA real → ADR futura cuando haga falta.
- Sin servicios managed — DBA, MQTT, etc. los gestionamos nosotros. A esta escala es ventaja (control + coste); a 100+ devices puede ser carga.
- Limit a EU para customer data (mitigación: el cliente no tiene PII crítica más allá de email + telemetría de su sistema).

### Neutras
- IPv6 nativo disponible (configurar Caddy y Mosquitto en consecuencia).

### Riesgos
- **Disco lleno por logs/telemetría** sin retention agresivo: ya configurado en TimescaleDB (90d raw, downsampling). Alerta si > 80% disco.
- **DDoS**: Hetzner tiene protección básica. Para algo serio → Cloudflare en frente.

## Referencias

- [Hetzner Cloud pricing](https://www.hetzner.com/cloud)
- [Caddy Server](https://caddyserver.com/)
- ADR-0004 (PostgreSQL + TimescaleDB)
- ADR-0007 (Mosquitto)
