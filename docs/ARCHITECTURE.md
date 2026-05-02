# Arquitectura — AcuaponicDuino v2.0

> **Documento maestro.** Si algo aquí cambia, todo lo demás se ajusta.
> Versión: 0.2 — pinout cerrado (N8R2, USB-Serial-JTAG). Última revisión: 2026-05-02.

---

## 1. Visión

Sistema de control y monitorización para acuaponía doméstica y semi-profesional.

- **Una unidad embebida** (ESP32-S3 sobre PCB custom 4 capas) controla todos los sensores y actuadores de un sistema acuapónico
- **Sensores Gravity/DFRobot** (BOM hobby), pero rodeados de electrónica industrial (ESD, filtrado, ADC externo, aislamiento)
- **Conectividad cloud opcional** multi-tenant. El sistema funciona en local sin internet
- **App web + app móvil** con dashboard, calibración guiada y notificaciones push
- **Asistente IA** (Claude API + tool use) que razona sobre los datos del usuario

---

## 2. Decisiones cerradas

| Capa | Tecnología | Razón |
|------|-----------|-------|
| MCU | ESP32-S3-WROOM-1 (N8R2) | Dual core, BLE 5, OTA, secure boot, ~3€ |
| Firmware | ESP-IDF v5.3+ + PlatformIO | FreeRTOS nativo, ecosistema sólido, OTA dual-partition |
| ADC externo | ADS1115 (I²C) | 16-bit, evita ruido del ADC interno del ESP32 |
| RTC | DS3231 + CR2032 | Timestamp correcto offline (crítico para HACCP) |
| Storage local | microSD (SPI, FAT32) | Log offline-resilient |
| PCB | KiCad 8, 4 capas | Plano de tierra continuo, EMC, futura certificación CE |
| Carcasa | IP65 ABS + cable glands + conectores M12 | Apta para entorno húmedo |
| Broker MQTT | Mosquitto 2 (TLS) | Estándar maduro, ligero |
| Base de datos | PostgreSQL 16 + TimescaleDB | Series temporales + relacional en una sola BD |
| Cache / queue | Redis 7 | Cache + cola para Arq |
| Backend | FastAPI (Python 3.12+) | Async, OpenAPI auto, fácil integración Claude API |
| Workers async | Arq | Más liviano que Celery a esta escala |
| Auth | JWT (access 15min + refresh 30d) | Sin estado, refresh token rotation |
| Web | Next.js 15 (App Router) + Tailwind v4 + shadcn/ui | Componentes accesibles, SSR opcional |
| Mobile | React Native + Expo SDK 52+ | Una codebase iOS+Android, OTA con EAS Update |
| BLE móvil | react-native-ble-plx | Maduro, multi-plataforma |
| IA | Claude API (`claude-sonnet-4-6`) + tool use + prompt caching | Razonamiento sobre datos del cliente |
| CI/CD | GitHub Actions + GHCR | Gratis hasta cuotas amplias, integrado con repo |
| Hosting | Hetzner Cloud (CX22 inicial, 5€/mes) | Suficiente para piloto |
| Proxy + TLS | Caddy 2 | HTTPS automático con Let's Encrypt |
| Monitoring | Grafana + Prometheus + Loki | Self-hosted, gratis |
| Docs | mkdocs-material | Estética, búsqueda full-text, fácil |
| Repo | Monorepo en GitHub | Atomic commits cross-stack |
| Licencia | **Por decidir** (MIT vs. AGPLv3) | MIT = adopción / AGPL = protección comercial |

---

## 3. Hardware: sensores, actuadores, asignación de pines

### 3.1 Sensores (entradas)

| Sensor | Tipo | Interfaz | Pin ESP32-S3 | Notas |
|--------|------|----------|--------------|-------|
| DS18B20 (temp. agua) | Digital | OneWire | GPIO 4 | Pull-up 4.7kΩ a 3.3V |
| Gravity pH v2 | Analógico (0-3.0V) | I²C vía ADS1115 ch0 | I²C: SDA=GPIO 8, SCL=GPIO 9 | Buffer op-amp + filtro RC |
| Gravity TDS | Analógico (0-2.3V) | I²C vía ADS1115 ch1 | (mismo bus) | Buffer + filtro |
| LDR PT550 (luz) | Analógico | ADS1115 ch2 | (mismo bus) | Divisor con R fija |
| SHT30 (T/HR ambiente) | I²C | I²C bus | (mismo bus) | Reemplaza el módulo analógico del v1 |
| FS400A entrada | Pulsos (frecuencia) | Interrupción + Schmitt 74HC14 | GPIO 5 | Pulso → litros/min |
| FS400A salida | Pulsos | Interrupción + Schmitt | GPIO 6 | Igual |
| FS-IR02 nivel min | Digital (open collector) | Interrupción | GPIO 7 | Pull-up 10kΩ |
| FS-IR02 nivel max | Digital | Interrupción | GPIO 15 | Pull-up 10kΩ |
| Botón START | Digital (anti-rebote HW) | Interrupción | GPIO 16 | RC + Schmitt |
| Botón STOP | Digital (anti-rebote HW) | Interrupción | GPIO 17 | RC + Schmitt |
| Botón RESET WiFi | Digital | GPIO normal | GPIO 18 | Mantener 5s reinicia provisioning |

### 3.2 Actuadores (salidas)

| Actuador | Canal relé | Pin ESP32-S3 | Carga típica |
|----------|------------|--------------|--------------|
| Bomba principal | RL1 | GPIO 35 | 230VAC ~50W |
| Calentador agua | RL2 | GPIO 36 | 230VAC ~100W |
| Compresor / aireador | RL3 | GPIO 37 | 230VAC ~10W |
| Electroválvula 1 (entrada) | RL4 | GPIO 38 | 12VDC |
| Electroválvula 2 | RL5 | GPIO 39 | 12VDC |
| Electroválvula 3 | RL6 | GPIO 40 | 12VDC |
| Electroválvula 4 (salida) | RL7 | GPIO 41 | 12VDC |
| Iluminación / aux | RL8 | GPIO 42 | configurable |

### 3.3 Periféricos del propio sistema

| Función | Pin |
|---------|-----|
| LED status power | GPIO 47 |
| LED WiFi | GPIO 48 |
| LED cloud | GPIO 21 |
| LED por relé (8) | a través de driver |
| microSD CS | GPIO 10 |
| microSD MOSI/MISO/SCK | GPIO 11/13/12 |
| RTC interrupt (alarma) | GPIO 14 |
| Watchdog HW reset | GPIO 1 |
| UART debug TX/RX | GPIO 43/44 (fallback de consola, USB-CDC primario) |
| USB-Serial-JTAG | GPIO 19/20 (compartido con USB-OTG nativo, sobre el mismo USB-C) |

> **Nota sobre muxing:** ESP32-S3 ofrece dos vías de debug — JTAG dedicado (GPIO 39-42) y USB-Serial-JTAG (GPIO 19/20, sobre USB-C nativo).
> Usamos exclusivamente USB-Serial-JTAG: libera GPIO 39-42 para los relés RL5-RL8, elimina un conector del panel y reduce BOM.
> Variante fija en **N8R2** (PSRAM quad, 2MB): deja GPIO 33-37 libres para relés.
> Las variantes con PSRAM octal (N8R8, N16R8) consumen GPIO 33-37 internamente y no son compatibles con este pinout.

---

## 4. Diagrama de bloques del PCB

```
   12-24V DC ──┬─→ Diodo polaridad inversa
               ├─→ TVS (SMAJ24CA)
               ├─→ Fusible PTC reseteable (MF-R030)
               │
               ├──→ Buck DC-DC a 5V (TPS5430)  ──→ rail relés (alimenta bobinas)
               │
               └──→ Buck DC-DC a 3.3V (MP1584) ──→ rail lógico
                      │
   ┌──────────────────┴──────────────────────────────────────────────┐
   │  ESP32-S3-WROOM-1 N8R2 (8MB flash, 2MB PSRAM quad, U.FL ext.)   │
   │  + cristal 40MHz externo (±10ppm)                               │
   │  + reset RC + boot button + USR button                          │
   │  + USB-C con TVS array (USBLC6-2SC6)                            │
   │  + USB-Serial-JTAG nativo (debug + flash sobre el mismo USB-C)  │
   └────┬───────────────────────┬─────────────────┬──────────────────┘
        │                       │                 │
   ┌────┴────────────┐    ┌─────┴────────┐  ┌─────┴────────────┐
   │ ANALOG FRONTEND │    │ DIGITAL I/O  │  │ COMMS + STORAGE  │
   │                 │    │              │  │                  │
   │ ADS1115 (I²C)   │    │ OneWire bus  │  │ microSD (SPI)    │
   │ + MCP6002 buffer│    │ FS400A x2    │  │ DS3231 RTC (I²C) │
   │ + RC filter     │    │ FS-IR02 x2   │  │ TPS3823 watchdog │
   │ + ESD (PESD3V3) │    │ Buttons x3   │  │ SIM tray (NP)    │
   │                 │    │              │  │ RJ45 PoE (NP)    │
   │ pH / TDS / LDR  │    │ Schmitt      │  │                  │
   │ SHT30 I²C       │    │ 74HC14       │  │ NP = no poblado  │
   └─────────────────┘    └──────────────┘  └──────────────────┘
                                  │
                          ┌───────┴────────────────────────────┐
                          │  RELAY OUTPUT BANK (8 canales)     │
                          │  + Optoacoplador EL357             │
                          │  + Driver ULN2803                  │
                          │  + Diodo flyback 1N4007            │
                          │  + LED status por canal            │
                          │  + Bornas Phoenix MKDS             │
                          └────────────────────────────────────┘
```

### Bornas externas

- **1× Power**: bornas 5.08mm para 12-24V DC
- **8× Conectores M12 4-pin codificados** en panel (un código por tipo de sensor):
  - Código A → sondas analógicas (pH, TDS)
  - Código B → temperatura (DS18B20)
  - Código C → caudal (FS400A)
  - Código D → nivel (FS-IR02)
- **8× Bornas Phoenix MKDS 5.08mm** para salidas de relé
- **1× Conector M12 8-pin** para botones externos opcionales
- **USB-C** accesible desde la carcasa (servicio)
- **Antena WiFi externa** SMA roscada en panel

---

## 5. Topics MQTT

Esquema renombrado y limpiado respecto al v1. Convención: `acuaponico/<org_id>/<device_id>/<categoria>/<sub>`.

### Telemetría (device → cloud)

| Topic | Payload | QoS |
|-------|---------|-----|
| `acuaponico/{org}/{dev}/telemetry/agua/temperatura` | `{"v":24.3,"ts":1745000000,"unit":"C"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/agua/ph` | `{"v":7.23,"ts":...,"unit":"pH"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/agua/tds` | `{"v":480,"ts":...,"unit":"ppm"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/ambiente/temperatura` | `{"v":21.2,"unit":"C"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/ambiente/humedad` | `{"v":58,"unit":"%RH"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/ambiente/luz` | `{"v":340,"unit":"lux_approx"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/flujo/entrada` | `{"v":3.2,"unit":"l/min"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/flujo/salida` | `{"v":3.0,"unit":"l/min"}` | 1 |
| `acuaponico/{org}/{dev}/telemetry/nivel` | `{"min":true,"max":false}` | 1 |

### Eventos (device → cloud)

| Topic | Payload |
|-------|---------|
| `acuaponico/{org}/{dev}/event/alert` | `{"code":"PUMP_FAILURE","severity":"critical","ts":...}` |
| `acuaponico/{org}/{dev}/event/state` | `{"state":"running"\|"stopped"\|"error"}` |
| `acuaponico/{org}/{dev}/event/calibration` | `{"sensor":"ph","points":[...],"ts":...}` |
| `acuaponico/{org}/{dev}/status` | `online` / `offline` (LWT, retain) |

### Comandos (cloud → device)

| Topic | Payload |
|-------|---------|
| `acuaponico/{org}/{dev}/cmd/start` | `{"subsystem":"flow"\|"water"\|"all"}` |
| `acuaponico/{org}/{dev}/cmd/stop` | `{"subsystem":...}` |
| `acuaponico/{org}/{dev}/cmd/relay` | `{"channel":3,"state":true}` |
| `acuaponico/{org}/{dev}/cmd/calibrate` | `{"sensor":"ph","point":7.0,"raw":1820}` |
| `acuaponico/{org}/{dev}/cmd/ota` | `{"url":"https://...","sha256":"...","version":"2.1.0"}` |
| `acuaponico/{org}/{dev}/config` | `{"cadence":{...},"thresholds":{...}}` (retain) |

### ACL

Cada dispositivo solo puede publicar/suscribirse a su propio prefijo. El backend tiene credenciales con acceso completo. Se aplica vía `mosquitto_dynsec`.

---

## 6. Esquema de base de datos (overview)

PostgreSQL 16 + TimescaleDB.

### Tablas relacionales (Postgres puro)

- `organizations` (id, name, plan, created_at)
- `users` (id, email, password_hash, role, org_id)
- `devices` (id, serial_number, org_id, name, location, fw_version, last_seen, cert_fingerprint)
- `sensor_calibrations` (device_id, sensor_type, points jsonb, valid_from, valid_to)
- `alert_rules` (id, device_id, sensor, condition, threshold, severity, enabled)
- `alerts_history` (id, device_id, code, severity, message, ts, acked_by, acked_at)
- `audit_log` (id, user_id, action, target, ts, ip, user_agent)
- `notification_channels` (id, user_id, type, config jsonb)
- `firmware_releases` (version, channel, url, sha256, signature, created_at)

### Hypertables (TimescaleDB)

- `telemetry` (device_id, ts, sensor_type, value double, unit) — particionada por tiempo (1 día)
- Continuous aggregates:
  - `telemetry_5m` (avg, min, max por 5min)
  - `telemetry_1h` (avg, min, max por hora)
  - `telemetry_1d` (avg, min, max por día)
- Retention policies:
  - raw: 90 días
  - 5min: 6 meses
  - 1h: 2 años
  - 1d: 5 años

### Índices clave

- `devices(org_id)`, `devices(serial_number unique)`
- `telemetry(device_id, ts DESC)`, `telemetry(sensor_type, ts DESC)`
- `alerts_history(device_id, ts DESC)` con WHERE `acked_at IS NULL`

---

## 7. Flujos clave

### 7.1 Provisioning del dispositivo

```
1. Fábrica: se inyecta cert X.509 único + clave privada en NVS protegido (factory test)
2. Usuario: enchufa la unidad. Si no hay WiFi configurado → modo BLE provisioning
3. Usuario: escanea QR del dispositivo (serial + token) con la app
4. App: se conecta vía BLE, transfiere SSID + password WiFi + URL del cloud
5. ESP32: persiste en NVS, se reinicia, conecta a WiFi
6. ESP32: presenta cert al broker MQTT (mTLS) + cert al backend para registro
7. Backend: valida cert, asigna a la org del usuario que lo registró, marca como activo
8. App: muestra el dispositivo listo para configurar
```

### 7.2 OTA

```
1. Backend publica nueva firmware con sha256 + firma + version
2. Usuario aprueba update desde la app (o auto si está habilitado)
3. Backend publica en `cmd/ota` con URL del binario
4. ESP32 descarga al partition inactivo
5. Verifica sha256 + firma RSA con clave pública grabada en flash
6. Marca partition como pendiente, reinicia
7. Bootloader carga la nueva versión
8. Si en 60s no hace `esp_ota_mark_app_valid_cancel_rollback()` → rollback automático
9. App nueva confirma OK al backend → release marcada como exitosa para ese device
```

### 7.3 Alerta crítica

```
1. ESP32 detecta condición (e.g. 5 lecturas seguidas de caudal=0)
2. Publica `event/alert` con código y severidad
3. ESP32 también activa LED rojo + buzzer (alerta local independiente del cloud)
4. Backend recibe, persiste en alerts_history, evalúa notification_channels
5. Backend dispara: push notification (FCM/APNs), email, Telegram según preferencias
6. Usuario abre la app → ve detalle, contexto histórico, acción sugerida por IA
7. Usuario hace ack → backend persiste acked_by/acked_at + audit_log
```

### 7.4 Calibración de pH

```
1. Usuario abre wizard en app → "Calibrar pH"
2. App: "sumerge sonda en buffer 7.0, espera 60s, pulsa siguiente"
3. ESP32 recibe `cmd/calibrate` con punto teórico, devuelve raw ADC
4. Repite para 4.0 y 10.0
5. ESP32 calcula recta + persiste en NVS + publica `event/calibration`
6. Backend persiste en sensor_calibrations
7. App muestra: "calibración OK, válida hasta 2026-06-15" (1 mes)
```

---

## 8. Modelo de seguridad

| Capa | Mecanismo |
|------|-----------|
| Firmware | Secure Boot v2 (ROM verifica firma RSA-3072 del bootloader) |
| Firmware | Flash Encryption (AES-XTS, clave en eFuse, no extraíble) |
| Identidad device | Cert X.509 único por dispositivo, clave privada en NVS protegido por flash encryption |
| Comunicación | mTLS sobre MQTT 5.0 (puerto 8883), cert del broker firmado por nuestra CA |
| API | HTTPS exclusivo, HSTS, certs de Let's Encrypt vía Caddy |
| Auth usuario | JWT access (15 min) + refresh (30 días) con rotation, password Argon2id |
| Authorization | RBAC: roles `org_admin`, `operator`, `viewer` |
| Multi-tenancy | Aislamiento a nivel de fila por `org_id` en todas las queries (RLS opcional) |
| Audit | Tabla `audit_log` para acciones críticas (cambios config, OTA, calibraciones, ack alertas) |
| Secrets | `.env` cifrado con `sops` + `age`, nunca commiteado |
| Rate limiting | 60 req/min por usuario, 1000/min por IP, configurable |
| OTA | Firma RSA-2048 verificada en device antes de aplicar |
| Backups | Postgres dump diario cifrado a S3 (Hetzner Object Storage) con retención 30 días |
| GDPR | Right-to-be-forgotten implementado en API (cascada por user_id) |

---

## 9. Fuera de alcance (v2.0)

Decisiones explícitas de **lo que NO se hará** en esta versión:

- ❌ Soporte LTE/4G (footprint en PCB sí, integración no)
- ❌ LoRaWAN para nodos satélite
- ❌ Cámara y visión por computadora
- ❌ Modelos ML on-device
- ❌ Integración HACCP / certificación alimentaria
- ❌ Multi-idioma más allá de ES + EN
- ❌ Plan freemium con límites comerciales (la app es funcional sin cloud)
- ❌ Marketplace de plugins / integraciones de terceros
- ❌ App de escritorio nativa
- ❌ Notebooks / SDK público para desarrolladores externos
- ❌ Soporte de RS-485 / Modbus para integración industrial
- ❌ App watchOS / wearOS

Estos quedan como candidatos para v3.0+, fuera del scope del refactor inicial.

---

## 10. Glosario

| Término | Definición |
|---------|-----------|
| Device | Una unidad física (PCB + carcasa + sensores). 1 device = 1 sistema acuapónico |
| Organization | Tenant del sistema. Un usuario puede pertenecer a varias |
| Cadencia | Periodo entre dos lecturas del mismo sensor (configurable hot, sin reflashear) |
| Provisioning | Proceso de configuración inicial de una unidad nueva |
| Telemetría | Datos numéricos de sensores enviados periódicamente |
| Evento | Mensaje no periódico (alerta, cambio de estado, calibración) |
| OTA | Over-The-Air firmware update |
| BOM | Bill of Materials — lista de componentes del PCB |
| LWT | Last Will and Testament (mensaje MQTT enviado cuando un cliente cae) |
| RBAC | Role-Based Access Control |
| HACCP | Hazard Analysis and Critical Control Points (norma alimentaria) |
| EMC | Electromagnetic Compatibility (normativa que el PCB debe cumplir) |
