# ADR-0002: ESP-IDF (con PlatformIO) en lugar de Arduino framework

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

Para programar el ESP32-S3 hay dos frameworks principales:

- **Arduino-ESP32**: capa por encima de ESP-IDF. Familiar, ecosistema enorme de librerías, "funciona". Ideal para prototipos.
- **ESP-IDF**: framework oficial de Espressif. FreeRTOS nativo, Kconfig, control fino sobre Bluetooth, OTA, secure boot, partitions, NVS, flash encryption.

El v2 tiene requisitos no triviales:
- OTA dual-partition con rollback automático.
- Secure Boot v2 + Flash Encryption.
- mTLS sobre MQTT 5 con cert + clave privada en NVS protegido.
- BLE 5 LE Secure Connections con Numeric Comparison.
- Watchdog hardware externo + task watchdog FreeRTOS por tarea.
- Coredump partition para debugging post-mortem.
- Configuración versionada con Kconfig.

Arduino-ESP32 expone parte de esto pero como _features experimentales_ o con APIs que limitan flexibilidad. ESP-IDF lo expone todo de primera mano.

### Opciones evaluadas

| Framework | Pros | Contras |
|-----------|------|---------|
| **ESP-IDF + PlatformIO** | Control total, FreeRTOS expuesto, todas las features de seguridad nativas, build reproducible, CI fácil | Curva de aprendizaje más alta, más boilerplate al principio |
| Arduino-ESP32 | Curva suave, librerías abundantes | OTA / Secure Boot / Flash Encryption opacos o limitados; menos control sobre tareas |
| ESP-IDF puro (sin PlatformIO) | Más cercano al "metal" | Pierdes el library manager y la integración VSCode multi-plataforma |
| Zephyr | Portabilidad multi-MCU | Soporte ESP32-S3 menos maduro, comunidad más pequeña en este ecosistema |
| Rust (esp-rs) | Memory safety, ergonomía moderna | Ecosistema BLE/MQTT/OTA aún en construcción para ESP32-S3, riesgo alto a 2026 |

## Decision

**Usamos ESP-IDF v5.3+ a través de PlatformIO** como framework de firmware.

- PlatformIO orquesta el build, gestiona toolchain, y permite tener `platformio.ini` reproducible en CI.
- ESP-IDF se actualiza siguiendo LTS de Espressif (v5.3 LTS ahora).
- Componentes propios en `firmware/components/` con `idf_component_register`.

## Consequences

### Positivas
- OTA, Secure Boot, Flash Encryption, NVS protegido, BLE 5 SC: todo nativo sin hacks.
- FreeRTOS expuesto directamente — control sobre prioridades, stack sizes, watchdogs.
- Kconfig (`sdkconfig.defaults`) como única fuente de configuración del build.
- CI con `pio run` reproducible en GitHub Actions.

### Negativas
- Onboarding de un colaborador nuevo es más empinado que con Arduino.
- Algunas librerías "Arduino-only" no se pueden usar tal cual (mitigación: ESP-IDF tiene equivalentes nativos para todo lo que necesitamos).
- Tiempo de build inicial mayor (~1 min full clean) — mitigable con cache de PlatformIO en CI.

### Riesgos
- Breaking changes entre versiones de ESP-IDF mayores. Mitigación: pinear versión exacta en `platformio.ini`, actualizar en PR dedicada con tests.

## Referencias

- [ESP-IDF Programming Guide v5.3](https://docs.espressif.com/projects/esp-idf/en/v5.3/esp32s3/index.html)
- [PlatformIO ESP-IDF integration](https://docs.platformio.org/en/latest/frameworks/espidf.html)
- ADR-0001 (ESP32-S3 como MCU)
