# ADR-0007: Mosquitto 2 como broker MQTT

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

Necesitamos un broker MQTT 5.0 con:
- TLS en puerto 8883.
- mTLS para autenticación de devices con certificado X.509.
- ACL por device (cada uno solo publica/se suscribe a su prefijo).
- LWT (Last Will and Testament) para detectar desconexiones.
- Bajo uso de RAM/CPU (vamos a coexistir con Postgres + Redis + backend en una CX22 5€/mes).
- Throughput estimado peak: 100 devices × 6 sensores × 1 Hz = 600 msg/s. Holgura para 10× sin sudar.

### Opciones evaluadas

| Broker | Pros | Contras |
|--------|------|---------|
| **Mosquitto 2** | Estándar de facto, EPL/EDL license, ~5MB RAM idle, mTLS nativo, `mosquitto_dynsec` para ACL dinámicas | Plugin ecosystem más limitado; WebSocket bridge requiere config extra |
| EMQX 5 | Cluster nativo, dashboard bonito, WebSocket out-of-the-box | RAM ~200MB+, overkill para 1 nodo, license Apache 2.0 con features Enterprise propietarias |
| HiveMQ Community | Profesional, observability buena | JVM = más RAM, ecosistema Java |
| AWS IoT Core | Managed | Vendor lock-in, coste por mensaje, choca con el principio "local-first" |
| NanoMQ | C, ligero, MQTT 5 | Comunidad pequeña, dynsec limitado |

## Decision

**Usamos Mosquitto 2** con TLS y `mosquitto_dynsec` plugin.

- **Puerto 8883** únicamente (no 1883).
- **mTLS**: cert del broker firmado por nuestra CA (`step-ca` o EasyRSA). Cada device tiene cert X.509 único.
- **ACL**: cada device limitado a su prefijo `acuaponico/{org_id}/{device_id}/#`. Backend con credenciales de acceso completo.
- **LWT**: cada device publica `online` / `offline` con retain en `acuaponico/{org}/{dev}/status`.
- **Persistence** habilitado (mensajes QoS > 0 sobreviven reinicio).
- **Logs** a stderr → Loki.

Configuración en `infra/mosquitto/`.

## Consequences

### Positivas
- Footprint mínimo: ~5MB RAM. Cabe holgado en CX22.
- 20 años de hardening en producción. Bugs de seguridad raros y bien documentados.
- mTLS y ACL configurables sin escribir código.
- Comunidad grande, debug abundante en stackoverflow / issues.

### Negativas
- Sin clustering nativo. Si un día necesitamos HA → migrar a EMQX o Mosquitto + bridging.
- Dashboard inexistente (mitigación: Grafana con Prometheus exporter de Mosquitto).
- WebSocket bridge requiere listener adicional configurado.

### Neutras
- License: EPL 2.0 / EDL 1.0. Compatible con AGPLv3 del proyecto (somos clientes del broker, no embebemos su código).

### Riesgos
- Si el caso de uso evoluciona a > 10k devices simultáneos, Mosquitto monolítico se queda corto. Mitigación: a esa escala migramos a EMQX cluster o Mosquitto bridges. Lejos de nuestro target inicial.

## Referencias

- [Mosquitto docs](https://mosquitto.org/documentation/)
- [Dynamic Security plugin](https://mosquitto.org/documentation/dynamic-security/)
- [ARCHITECTURE.md §5 (Topics MQTT)](../ARCHITECTURE.md#5-topics-mqtt)
- [ARCHITECTURE.md §8 (Modelo de seguridad)](../ARCHITECTURE.md#8-modelo-de-seguridad)
