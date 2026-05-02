# Firmware

Documentación del firmware ESP-IDF + PlatformIO.

!!! info "Estado"
    Vacío. Se rellena en **M2** del [Roadmap](../ROADMAP.md).

## Contenido previsto

- Build instructions (PlatformIO + ESP-IDF v5.3+)
- Estructura de componentes (`hal/`, `drivers/`, `app/`, `comms/`, `storage/`, `util/`)
- Arquitectura de tareas FreeRTOS y prioridades
- Guía de cada driver (DS18B20, ADS1115, pH, TDS, SHT30, FS400A, FS-IR02)
- Protocolo MQTT (topics, payloads, QoS) — referencia cruzada con [`ARCHITECTURE §5`](../ARCHITECTURE.md#5-topics-mqtt)
- Secure Boot v2 + Flash Encryption: cuándo activar, cómo aprovisionar
- OTA dual-partition con rollback: flujo paso a paso
- BLE provisioning: GATT services y secuencia de configuración
- Calibración multipunto y persistencia en NVS
- Watchdogs (task watchdog + hardware externo)
- Coredump y debugging post-mortem
