# ADR-0001: ESP32-S3 como MCU único

- **Status**: Accepted
- **Date**: 2026-05-02
- **Deciders**: @asturnando

## Context

El TFG original (v1, 2022) usaba **dos placas**: Arduino Mega 2560 (lógica y sensores) + ESP8266 (WiFi, MQTT). Esa arquitectura arrastraba:

- Protocolo serial custom entre Mega y ESP8266 — fuente persistente de bugs.
- Dos firmwares que mantener.
- Dos crystals, dos reguladores, dos resets — más BOM, más fallos.
- ESP8266 sin soporte para mTLS robusto, sin BLE, sin OTA dual-partition.
- Mega 2560 con AVR de 16 MHz, sin FreeRTOS oficial estable, sin secure boot.

Para v2 necesitamos: WiFi + BLE, mTLS, OTA con rollback, FreeRTOS robusto, secure boot, flash encryption, USB-CDC nativo, dual-core para separar comms de control.

### Opciones evaluadas

| MCU | Pros | Contras |
|-----|------|---------|
| **ESP32-S3** | Dual-core Xtensa LX7 240MHz, BLE 5, WiFi, USB-OTG nativo, USB-Serial-JTAG, secure boot v2, flash encryption AES-XTS, ~3€, ESP-IDF maduro | Toolchain Xtensa específico (no GCC ARM); algunos bugs históricos en USB |
| ESP32-C6 | RISC-V, WiFi 6, Thread/Zigbee | Mono-core, ecosistema más nuevo, menos PSRAM, peor para nuestro workload |
| Raspberry Pi Pico W (RP2040) | Comunidad enorme, Pico SDK | WiFi vía CYW43439 (más pins, más coste), sin secure boot, sin BLE 5 nativo |
| STM32H7 + módulo WiFi externo | Mucho rendimiento | Coste 4-6× ESP32, módulo WiFi externo añade BOM, sin BLE integrado |
| Mantener Mega + ESP8266 | Familiar | Todos los problemas del v1 sin resolver |

## Decision

**Usamos ESP32-S3-WROOM-1 N8R2 como MCU único** del sistema.

- Variante **N8R2** (8MB flash, 2MB PSRAM quad). Las variantes octal (N8R8, N16R8) consumen GPIO 33-37 internamente y no son compatibles con el pinout. Ver [ARCHITECTURE §3](../ARCHITECTURE.md#3-hardware-sensores-actuadores-asignación-de-pines).
- Antena U.FL externa con conector SMA en panel (mejor RF que antena PCB para entornos con humedad/metal).
- Debug: **USB-Serial-JTAG nativo** sobre USB-C — sin header SWD/JTAG dedicado.

## Consequences

### Positivas
- Una sola placa, un solo firmware, un solo build.
- WiFi + BLE + USB-OTG con un único chip.
- Secure boot + flash encryption preparados para certificación CE.
- OTA dual-partition con rollback automático nativo.
- Dual-core permite separar tareas de comms (core 1) y control (core 0) sin contención.
- Coste BOM ~3€/unidad.

### Negativas
- Toolchain Xtensa requiere PlatformIO o ESP-IDF — no compila con GCC ARM estándar.
- 2MB PSRAM puede quedarse corto si se complica el firmware (mitigable con buffering en microSD).
- Strap pins (GPIO 0, 3, 45, 46) requieren cuidado en el layout.

### Riesgos
- **Lockout por Secure Boot mal configurado**: irreversible. Mitigación: mantener al menos una unidad sin securizar para desarrollo, activar secure boot solo en builds de producción finales.
- **Cambio de variante (a N16R8) requiere rediseño**: pinout no compatible. Mitigación: documentado claramente; v3 si hace falta.

## Referencias

- [Datasheet ESP32-S3-WROOM-1](https://www.espressif.com/sites/default/files/documentation/esp32-s3-wroom-1_wroom-1u_datasheet_en.pdf)
- [ESP32-S3 Technical Reference Manual](https://www.espressif.com/sites/default/files/documentation/esp32-s3_technical_reference_manual_en.pdf)
- [ARCHITECTURE.md §3](../ARCHITECTURE.md#3-hardware-sensores-actuadores-asignación-de-pines)
- ADR-0002 (ESP-IDF vs Arduino framework)
