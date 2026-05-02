# firmware/

Firmware del ESP32-S3 sobre ESP-IDF v5.3+ y PlatformIO.

## Estado

Vacío. Se llenará a partir de **M2** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
firmware/
├── platformio.ini          # entry point del build
├── partitions.csv          # factory + ota_0/1 + nvs + nvs_keys + storage
├── sdkconfig.defaults      # Kconfig por defecto
├── main/
│   └── app_main.c          # entry point
├── components/
│   ├── hal/                # abstracción GPIO, I²C, SPI, OneWire
│   ├── drivers/            # DS18B20, ADS1115, pH, TDS, SHT30, FS400A, FS-IR02, relés
│   ├── app/                # lógica de negocio, state machine
│   ├── comms/              # WiFi, MQTT (TLS), BLE provisioning, OTA
│   ├── storage/            # NVS, microSD, calibración persistente
│   └── util/               # logging, time, helpers
├── test/                   # unit tests con Unity
└── docs/                   # Doxygen output
```

## Build (cuando exista código)

```bash
cd firmware
pio run                     # build
pio run -t upload           # flash via USB-Serial-JTAG sobre USB-C
pio device monitor          # consola serie (115200 baud)
pio test                    # unit tests
```

## Notas críticas

- **Variante MCU**: ESP32-S3-WROOM-1 **N8R2** (PSRAM quad). Las variantes octal (N8R8/N16R8) no son compatibles con el pinout actual — ver [ARCHITECTURE §3](../docs/ARCHITECTURE.md).
- **Debug**: USB-Serial-JTAG nativo sobre el USB-C del PCB. No hay header SWD/JTAG dedicado.
- **Secure Boot v2 + Flash Encryption**: solo en builds de producción. NUNCA habilitar en una placa de desarrollo (es irreversible).
