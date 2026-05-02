# infra/

Infraestructura: Docker, despliegue Hetzner, Caddy, Mosquitto, monitoring, backups.

## Estado

Vacío. Se llenará a partir de **M3.5** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
infra/
├── docker-compose.dev.yml          # postgres, redis, mosquitto, mailhog, backend, worker
├── docker-compose.prod.yml         # + caddy, sin volumes locales
├── caddy/
│   └── Caddyfile                   # api.*, app.*, docs.*
├── mosquitto/
│   ├── mosquitto.conf
│   ├── dynsec.json                 # ACL inicial
│   └── ca/                         # scripts step-ca para certs de devices (gitignored secretos)
├── postgres/
│   └── init/                       # CREATE EXTENSION timescaledb, etc.
├── grafana/
│   └── dashboards/                 # JSON exportados
├── prometheus/
│   └── prometheus.yml
├── loki/
│   └── loki-config.yaml
├── scripts/
│   ├── deploy.sh                   # SSH a Hetzner + docker compose pull && up
│   ├── backup.sh                   # pg_dump | age | hcloud object upload
│   └── restore.sh
└── terraform/                      # opcional, para reproducibilidad de la VPS
```

## Hosting

- **VPS**: Hetzner Cloud, CX22 inicial (~5€/mes), Falkenstein.
- **Firewall**: 22 SSH, 80/443 HTTPS, 8883 MQTTS.
- **TLS**: Caddy con Let's Encrypt automático.
- **Backups**: `pg_dump` cifrado con `age` → Hetzner Object Storage, retención 30d.

## Despliegue

```bash
# Una sola vez
ssh root@<hetzner-ip>
bash <(curl -sL https://raw.githubusercontent.com/asturnando/acuaponicoduino/main/infra/scripts/setup-hetzner.sh)

# En cada release
./infra/scripts/deploy.sh production
```

## Secretos

`.env` cifrado con `sops` + `age`. La clave privada **nunca** se commitea — está fuera del repo, sincronizada por canales separados.
