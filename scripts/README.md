# scripts/

Scripts de desarrollo, operación y mantenimiento que no encajan en ninguna app concreta.

## Estado

Vacío. Se irán añadiendo según haga falta.

## Convenciones

- **Lenguaje**: bash POSIX cuando sea posible. Python 3.12+ si necesita lógica no trivial.
- **Header obligatorio**: descripción + uso + ejemplos.
- **`set -euo pipefail`** en todos los bash scripts.
- **Idempotencia**: re-ejecutar un script no debe romper nada.
- **Sin secretos hardcoded**: leer de `.env` o argumentos.

## Scripts previstos

| Script | Propósito |
|--------|-----------|
| `bootstrap-dev.sh` | Setup completo de entorno de desarrollo (deps, hooks, docker compose) |
| `release.sh` | Bump version, tag, push, dispara workflow de release |
| `provision-device.py` | Inyecta cert único en una unidad nueva (M2.18 — fábrica) |
| `gen-firmware-key.sh` | Genera par RSA-2048 para firmar OTA |
| `seed-db.py` | Pobla DB de dev con org, user y device de prueba |
| `import-tfg-data.py` | (Opcional) Migra logs históricos del TFG v1 a TimescaleDB |
