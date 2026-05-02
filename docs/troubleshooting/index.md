# Troubleshooting

!!! info "Estado"
    Se irá llenando con cada incidente real desde el bringup en adelante.

## Estructura prevista

- **Hardware**
    - PCB no enciende
    - Buck converter no entrega 3.3V / 5V
    - I²C scan no detecta dispositivos
    - microSD no monta
- **Firmware**
    - WiFi no conecta tras provisioning
    - MQTT desconexiones recurrentes
    - OTA falla en verificación
    - Drift del RTC tras corte de luz
- **Backend**
    - Migraciones Alembic falladas
    - Bridge MQTT pierde mensajes en alta cadencia
    - Tests e2e timeout
- **Web / Mobile**
    - WebSocket no se mantiene en background
    - BLE provisioning falla en Android < 12
    - Push notifications no llegan en iOS

Cada caso documenta: síntoma, causa raíz, solución, referencia a issue/commit.
