# Roadmap — AcuaponicDuino v2.0

> **Checklist exhaustiva.** Cada `- [ ]` es una tarea concreta. Marca `- [x]` al completar. Esta es la fuente de verdad del progreso.
> Versión: 0.1 — borrador inicial. Última revisión: 2026-05-02.

---

## Estimación global

| Milestone | Esfuerzo (fines de semana) | Wall clock realista |
|-----------|---------------------------|---------------------|
| M0 — Foundation | 2-3 | 2-3 semanas |
| M1 — Hardware | 8-12 | 2-3 meses (con tiempos de envío PCB) |
| M2 — Firmware | 12-16 | 3-4 meses |
| M3 — Cloud Backend | 8-10 | 2-3 meses |
| M4 — Frontend (web + móvil) | 10-14 | 3-4 meses |
| M5 — Diferenciadores (IA, UX) | 4-6 | 1-2 meses |
| M6 — Lanzamiento | 4-6 | 1-2 meses |
| **Total** | **48-67 fines de semana** | **~12-18 meses** a 1 fin de semana/semana |

> **Ruta crítica**: M0 → M1 (esquemático) → M2 (firmware MVP) en paralelo con M3 (backend) → M4 → M5 → M6.
> M1 (PCB) y M2 (firmware) pueden solaparse desde el momento en que el esquemático esté validado.

---

# M0 — Foundation

> **Objetivo**: tener todo el andamiaje del proyecto listo antes de escribir una sola línea funcional. Es la diferencia entre un proyecto serio y uno que muere a la mitad.

## Fase 0.1 — Setup del repositorio

- [x] Decidir licencia (MIT / Apache 2.0 / AGPLv3) y añadir `LICENSE` → **AGPLv3**
- [x] Crear repo público en GitHub: [`asturnando/acuaponicoduino`](https://github.com/asturnando/acuaponicoduino)
- [x] `git init`, primer commit con README + ARCHITECTURE + ROADMAP
- [x] Configurar branch protection en `main` (PR required, 0 approvers — solo dev, conversation resolution required, no force pushes, no deletions)
- [x] Configurar `CODEOWNERS`
- [x] `.gitignore` global (Python, Node, KiCad, ESP-IDF, build artifacts, secretos)
- [x] `.gitattributes` (LFS para gerbers, .step, imágenes grandes)
- [x] `.editorconfig` (indentación, EOL, trim trailing whitespace)
- [x] `pre-commit-config.yaml`: ruff, prettier, mypy, eslint, gitleaks, conventional-pre-commit (kicad-cli erc/drc se añadirá en M1 cuando exista PCB)
- [x] Plantillas de issue (bug, feature, hardware-issue)
- [x] Plantilla de PR
- [x] `CONTRIBUTING.md` (aunque sea solo para ti, define convenciones)
- [x] `SECURITY.md` (proceso de disclosure, aunque básico)
- [x] `CODE_OF_CONDUCT.md`
- [x] `CHANGELOG.md` (formato Keep a Changelog)
- [x] Estructura de monorepo: `firmware/`, `hardware/`, `backend/`, `web/`, `mobile/`, `infra/`, `ai/`, `docs/`, `scripts/`
- [x] `README.md` por subdirectorio explicando qué hay dentro

## Fase 0.2 — CI/CD baseline

- [x] GitHub Actions: workflow `ci.yml` (paths-filter, pre-commit, gitleaks, summary)
- [x] Workflow `firmware-build.yml` (skeleton con auto-skip si `firmware/` vacío)
- [x] Workflow `backend-test.yml` (skeleton con servicios postgres+redis, auto-skip si `backend/` vacío)
- [x] Workflow `web-build.yml` (skeleton con pnpm, auto-skip si `web/` vacío)
- [x] Workflow `docs-deploy.yml` para mkdocs → GitHub Pages
- [x] Workflow `release.yml` (extrae notas del CHANGELOG, crea GitHub Release)
- [x] Configurar Dependabot (`dependabot.yml`) para Python, Node, GitHub Actions, Docker
- [x] Activar CodeQL (workflow `.github/workflows/codeql.yml` con queries security-extended para Python, JS/TS y GitHub Actions)
- [x] Activar secret scanning + push protection + Dependabot security updates + vulnerability alerts

## Fase 0.3 — Documentación viva

- [x] `mkdocs.yml` con tema material
- [x] Estructura `docs/`: arquitectura, hardware, firmware, backend, web, mobile, manual-usuario, manual-instalacion, api, troubleshooting
- [x] Mermaid habilitado vía `pymdownx.superfences` (diagramas concretos se añaden cuando existan)
- [x] Configurar deploy automático a GitHub Pages
- [ ] Dominio personalizado opcional (`docs.acuaponicoduino.com`) _(post-M6)_

## Fase 0.4 — Project management

- [ ] Crear GitHub Project (board) con vistas: Backlog / In Progress / Review / Done _(GraphQL API — pendiente, no bloqueante)_
- [ ] Importar todas las tareas de este roadmap como issues etiquetadas por milestone _(decisión: solo abrir issues de tareas activas, no de las 200+ futuras — evita ruido)_
- [x] Crear milestones M0..M6 en GitHub (8 milestones: M0, M1, M2, M3, M3.5, M4, M5, M6)
- [x] Crear sistema de labels (component:, type:, priority:, status:)
- [x] Definir convención de commit messages (Conventional Commits) → en `CONTRIBUTING.md`
- [x] Definir convención de branching (`feat/`, `fix/`, `docs/`, `hw/`, `fw/`, etc.) → en `CONTRIBUTING.md`

## Fase 0.5 — Decisiones de arquitectura escritas (ADRs)

- [x] Crear `docs/adr/` con plantilla
- [x] ADR-001: ESP32-S3 vs Mega+ESP8266
- [x] ADR-002: ESP-IDF vs Arduino framework
- [x] ADR-003: FastAPI vs NestJS
- [x] ADR-004: PostgreSQL+TimescaleDB vs InfluxDB vs Mongo
- [x] ADR-005: Next.js vs Remix vs SvelteKit
- [x] ADR-006: React Native+Expo vs Flutter
- [x] ADR-007: Mosquitto vs EMQX vs HiveMQ
- [x] ADR-008: Monorepo vs poly-repo
- [x] ADR-009: Licencia (AGPLv3)
- [x] ADR-010: Hosting (Hetzner Cloud)

**Entregable de M0**: repositorio listo, CI verde aunque sin código real, documentación accesible online, project board con todas las tareas, ADRs escritas.

---

# M1 — Hardware

> **Objetivo**: tener PCB v0.1 fabricado, ensamblado, con todos los periféricos verificados.

## Fase 1.1 — Especificaciones eléctricas

- [ ] Listar requisitos eléctricos por entrada/salida (V, I, impedancia, ancho de banda)
- [ ] Definir rangos operativos (12-24V DC, -10°C a +50°C, 0-95% HR no condensante)
- [ ] Estimar consumo total worst-case (todos los relés ON + WiFi TX)
- [ ] Calcular dimensionamiento de buck converters (margen 50%)
- [ ] Definir estrategia de protección (TVS, fusibles, polaridad)
- [ ] Definir estrategia EMC (plano de tierra, separación analógico/digital, ferrites)
- [ ] Confirmar pinout del ESP32-S3 y validar muxing (no conflictos JTAG/USB/SPI)

## Fase 1.2 — KiCad: librerías y proyecto

- [ ] Instalar KiCad 8 estable
- [ ] Crear proyecto `hardware/kicad/acuaponicoduino-main/`
- [ ] Configurar librería de proyecto (símbolos, footprints, 3D models)
- [ ] Importar símbolos desde SnapEDA/Ultralibrarian para componentes propietarios
- [ ] Crear símbolo + footprint para conector M12 4-pin (si no está en libs estándar)
- [ ] Verificar todos los footprints contra datasheet (margen pads, drill, courtyard)

## Fase 1.3 — Esquemático: alimentación

- [ ] Hoja `power.kicad_sch`
- [ ] Entrada 12-24V con borna 5.08mm
- [ ] Diodo Schottky de polaridad inversa (SS54 o similar)
- [ ] TVS bidireccional SMAJ24CA
- [ ] Fusible PTC reseteable MF-R030 (o equivalente para 3A)
- [ ] Filtro EMI (ferrite bead BLM21)
- [ ] Buck a 5V: TPS5430DDA + LC output filter
- [ ] Buck a 3.3V: MP1584EN + LC output filter
- [ ] LDOs locales si se requiere ruido bajo (e.g. AP2112 para analógico)
- [ ] Test points en cada rail
- [ ] LED de power
- [ ] ERC limpio para esta hoja

## Fase 1.4 — Esquemático: MCU

- [ ] Hoja `mcu.kicad_sch`
- [ ] ESP32-S3-WROOM-1 N8R2 (PSRAM quad — variantes octal no compatibles con este pinout)
- [ ] Decoupling: 100nF cerca de cada VDD + 10µF bulk
- [ ] Cristal externo 40MHz ±10ppm con loading caps
- [ ] Reset RC + diodo + botón reset
- [ ] Boot button (GPIO0) + USR button
- [ ] USB-C con CC1/CC2 pull-downs + ESD (USBLC6-2SC6)
- [ ] USB-Serial-JTAG nativo del ESP32-S3 sobre USB-C (sin header dedicado)
- [ ] Header UART debug 6-pin (TX, RX, GND, 3.3V, RESET, IO0) como fallback
- [ ] LED status power (3.3V) + LED USR
- [ ] Antena U.FL externa con conector SMA en panel
- [ ] Strap pins respetados (GPIO0, GPIO45, GPIO46)
- [ ] ERC limpio

## Fase 1.5 — Esquemático: analog frontend

- [ ] Hoja `analog.kicad_sch`
- [ ] ADS1115 con dirección I²C configurable (resistor strap)
- [ ] Decoupling y referencia
- [ ] Op-amp MCP6002 dual como buffer de impedancia
- [ ] Filtro RC pasa-bajos (fc ~10Hz para sondas químicas)
- [ ] Protección ESD con TVS (PESD3V3L1BA) en cada entrada
- [ ] Resistencia serie de protección (1kΩ)
- [ ] Conector M12 código A para pH (señal + GND + shield + 5V opcional)
- [ ] Conector M12 código A para TDS
- [ ] Divisor + buffer para LDR PT550
- [ ] SHT30 I²C (con dirección configurable, alimentación, decoupling)
- [ ] ERC limpio

## Fase 1.6 — Esquemático: digital sensors

- [ ] Hoja `digital_in.kicad_sch`
- [ ] DS18B20 OneWire: pull-up 4.7kΩ, conector M12 código B
- [ ] FS400A x2: filtro RC + Schmitt 74HC14, conector M12 código C cada uno
- [ ] FS-IR02 x2: pull-up 10kΩ + filtro, conector M12 código D
- [ ] Botones físicos START/STOP/RESET con anti-rebote RC + Schmitt
- [ ] Cada entrada con protección ESD
- [ ] ERC limpio

## Fase 1.7 — Esquemático: relay outputs

- [ ] Hoja `relay_out.kicad_sch`
- [ ] 8 canales idénticos:
  - Optoacoplador EL357N (aislamiento lógica/potencia)
  - Driver darlington ULN2803 o transistor 2N7002 + pull-up
  - Relé SRD-05VDC-SL-C (10A, contactos NC/NO)
  - Diodo flyback 1N4007 antiparalelo
  - LED rojo de estado por canal con resistencia
  - Borna Phoenix MKDS 5.08mm 3-pin (NO/COM/NC)
  - Snubber RC opcional para cargas inductivas
- [ ] Plano de tierra de relés separado de tierra lógica, unidos en un solo punto (star ground)
- [ ] ERC limpio

## Fase 1.8 — Esquemático: comms y storage

- [ ] Hoja `comms.kicad_sch`
- [ ] microSD socket (push-push) con SPI + level shifter si necesario
- [ ] DS3231 RTC con CR2032 holder + I²C
- [ ] Watchdog hardware TPS3823 con conexión a EN o RESET del MCU
- [ ] Footprint para SIM tray (no poblado, reserva v3)
- [ ] Footprint para magjack RJ45 (no poblado, reserva)
- [ ] ERC limpio

## Fase 1.9 — Esquemático: integración

- [ ] Hoja top con jerarquía (todas las anteriores como sub-hojas)
- [ ] Net labels coherentes entre hojas
- [ ] Bus I²C compartido con pull-ups únicos (4.7kΩ) en hoja top
- [ ] ERC global limpio
- [ ] Generar netlist
- [ ] Revisar BOM (componentes únicos, agrupados, con MPN)

## Fase 1.10 — Componentes y BOM

- [ ] BOM final en CSV con: Reference, Quantity, MPN, Manufacturer, Description, Distributor, DistributorPN, Price
- [ ] Verificar disponibilidad de cada parte en Mouser/Digikey/LCSC
- [ ] Backup MPN para los 5 componentes más críticos
- [ ] Marcar componentes "Place" vs "Do Not Populate" (DNP)
- [ ] Estimar coste por unidad a 1, 10, 100 y 1000 uds

## Fase 1.11 — PCB Layout: stackup y reglas

- [ ] Decidir stackup 4 capas (Signal / GND / PWR / Signal)
- [ ] Especificar grosor (1.6mm estándar, 1.0mm si conflicto mecánico)
- [ ] Configurar reglas de diseño en KiCad: clearance, track width, vias
- [ ] Definir clases de net: power (8mil), signal (6mil), USB (diff pair)
- [ ] Configurar reglas para JLCPCB (capabilities mínimas)

## Fase 1.12 — PCB Layout: placement

- [ ] Definir contorno (outline) según carcasa elegida
- [ ] Posicionar conectores en panel (M12, USB-C, bornas, antena)
- [ ] Posicionar ESP32 y crystal cerca con keepout de antena
- [ ] Posicionar zona analógica separada de zona de relés
- [ ] Posicionar ADS1115 cerca de op-amps y conectores analógicos
- [ ] Posicionar buck converters lejos de zona analógica
- [ ] Posicionar relés alineados con bornas de salida
- [ ] Verificar 3D fit en carcasa con `kicad-cli pcb export step`

## Fase 1.13 — PCB Layout: routing

- [ ] Rutar power primero (rails 3.3V, 5V, GND)
- [ ] Plano de GND continuo en capa 2
- [ ] Plano de PWR en capa 3 (3.3V dominante)
- [ ] Rutar señales críticas: USB diff pair (90Ω, longitud emparejada)
- [ ] Rutar crystal cortito y guarded
- [ ] Rutar SPI a microSD lejos de switching
- [ ] Rutar I²C corto, agrupado, con pull-ups visibles
- [ ] Vias adecuadas (0.3mm drill mínimo para JLCPCB low-cost)
- [ ] Mantener distancia entre tracks de relé AC y lógica (≥3mm)
- [ ] Stitching vias en plano de GND
- [ ] DRC limpio (cero errores)
- [ ] Verificar copper pour conectado correctamente

## Fase 1.14 — PCB Layout: silk y assembly

- [ ] Silkscreen legible (mínimo 0.8mm altura, no debajo de pads)
- [ ] Marcar polaridad de diodos, electrolíticos, conectores
- [ ] Marcar pin 1 de cada IC
- [ ] Numeración de relés visible en bornas
- [ ] Logo del proyecto + versión PCB ("v0.1") + fecha
- [ ] Notas de assembly (e.g. "DNP", marcas de orientación)

## Fase 1.15 — Verificación pre-fabricación

- [ ] Generar 3D y revisar visualmente en `kicad-cli pcb export step`
- [ ] Generar render top y bottom para review
- [ ] Print 1:1 en papel y verificar tamaño físico de componentes
- [ ] DRC final limpio
- [ ] ERC final limpio
- [ ] Pedir a alguien con experiencia (o a mí en review) que revise el esquema y layout

## Fase 1.16 — Fabricación

- [ ] Generar Gerbers + drill files (X2 format)
- [ ] Generar Pick & Place (top + bottom)
- [ ] Generar BOM compatible con JLCPCB Assembly Service
- [ ] Subir a JLCPCB y revisar previsualización
- [ ] Pedir 5 unidades PCB con SMT assembly (componentes pequeños) → ~80-150€
- [ ] Pedir componentes through-hole y conectores aparte (Mouser/Digikey)
- [ ] Esperar 1-3 semanas

## Fase 1.17 — Diseño de carcasa

- [ ] Decidir entre carcasa off-the-shelf vs. custom
- [ ] Si custom: modelar en FreeCAD/Fusion 360
- [ ] Cutouts para: 8 conectores M12, USB-C, antena SMA, panel de bornas relé, LEDs frontales
- [ ] Mounting bosses para PCB
- [ ] Cable glands para entrada de cables externos
- [ ] Tapa con tornillos M3 + junta tórica para IP65
- [ ] Imprimir prototipo en SLA (resina transparente para ver dentro) o PETG
- [ ] Validar fit con PCB v0.1 antes de fabricar definitivo

## Fase 1.18 — Bringup hardware

- [ ] Inspección visual con lupa al recibir PCB
- [ ] Medir resistencia entre 3.3V y GND antes de aplicar potencia (>1kΩ)
- [ ] Medir resistencia entre 5V y GND antes de aplicar potencia
- [ ] Aplicar 12V con limitador de corriente (500mA max inicial)
- [ ] Verificar 5V rail con multímetro (4.95-5.05V)
- [ ] Verificar 3.3V rail (3.27-3.33V)
- [ ] Verificar consumo en idle <100mA
- [ ] Conectar USB-C, ver enumeración como puerto serie
- [ ] Flashear bootloader ESP-IDF "hello world"
- [ ] Verificar log por UART
- [ ] Verificar acceso a flash (write/read NVS)

## Fase 1.19 — Verificación de cada periférico

- [ ] LEDs: blink test cada uno
- [ ] Botones: leer estado, verificar anti-rebote
- [ ] I²C: scan, ver direcciones de ADS1115, SHT30, DS3231
- [ ] OneWire: enumerar DS18B20, leer temperatura
- [ ] SPI microSD: montar FS, escribir/leer fichero
- [ ] FS400A: contar pulsos con pulse generator
- [ ] FS-IR02: simular activación con resistencia
- [ ] Relés: activar cada canal, oír clic, medir continuidad
- [ ] WiFi: conectar a AP y hacer ping
- [ ] BLE: advertise visible desde móvil
- [ ] USB: enumeración + log estable
- [ ] Watchdog: provocar reset por inactividad

## Fase 1.20 — Errata y v0.2 si aplica

- [ ] Documentar todos los problemas encontrados en `hardware/ERRATA.md`
- [ ] Categorizar: blocker (impide funcionamiento), workaround posible, cosmético
- [ ] Si hay blockers → diseñar PCB v0.2 con fixes
- [ ] Si solo workarounds → seguir con v0.1 + reworks documentados

**Entregable M1**: 5 PCBs ensambladas, una de ellas con todos los periféricos verificados, errata documentada, BOM cerrada, carcasa prototipo lista.

---

# M2 — Firmware

> **Objetivo**: firmware que lee todos los sensores, controla todos los actuadores, se conecta a MQTT con TLS, soporta OTA y BLE provisioning, todo con calidad production-grade.

## Fase 2.1 — Setup PlatformIO + ESP-IDF

- [ ] Instalar PlatformIO core
- [ ] Crear `firmware/platformio.ini` con framework `espidf`, board `esp32-s3-devkitc-1`
- [ ] Configurar partitions custom (`partitions.csv`): factory + ota_0 + ota_1 + nvs + nvs_keys + spiffs/storage
- [ ] Configurar `sdkconfig.defaults` (Secure Boot OFF inicialmente, Flash Encryption OFF inicialmente)
- [ ] Verificar build "hello world" + upload
- [ ] Configurar VSCode con extensión PlatformIO IDE
- [ ] Configurar `clangd` LSP

## Fase 2.2 — Estructura de proyecto

- [ ] `main/` con `app_main`
- [ ] `components/` para cada módulo:
  - `hal/` (abstracción de hardware)
  - `drivers/` (sensores, actuadores)
  - `app/` (lógica de negocio)
  - `comms/` (WiFi, MQTT, BLE, OTA)
  - `storage/` (NVS, microSD)
  - `util/` (logging, time, helpers)
- [ ] `CMakeLists.txt` por componente con `idf_component_register`
- [ ] Configurar `.clang-format` (Google C++ style adaptado)
- [ ] Configurar logging (`esp_log` con niveles por componente)

## Fase 2.3 — Logging y observabilidad básica

- [ ] Wrapper `LOG_*` con tags consistentes
- [ ] Niveles configurables vía Kconfig (DEBUG en debug builds, INFO en release)
- [ ] Logs estructurados (JSON line) opcionales
- [ ] Heap stats en log periódico
- [ ] Stack high water mark por tarea

## Fase 2.4 — HAL: GPIO, I²C, SPI, OneWire

- [ ] Wrapper GPIO con ISR-safe API
- [ ] Bus I²C compartido con mutex
- [ ] Bus SPI para microSD
- [ ] OneWire bit-banged o RMT-based
- [ ] Tests unitarios donde sea posible (mock de bus)

## Fase 2.5 — Drivers de sensores

- [ ] Driver DS18B20 (lectura, conversión, error handling)
- [ ] Driver ADS1115 (config, lectura single-ended, conversión raw→volts)
- [ ] Driver pH:
  - Lectura raw via ADS1115
  - Aplicación de curva de calibración (almacenada en NVS)
  - Compensación con temperatura del agua
  - Fault detection (raw fuera de rango físico)
- [ ] Driver TDS:
  - Lectura raw + compensación temperatura
  - Conversión a ppm
- [ ] Driver LDR (curva linealización)
- [ ] Driver SHT30 (I²C, CRC8 verificación)
- [ ] Driver FS400A (pulse counter via PCNT peripheral, no interrupciones)
- [ ] Driver FS-IR02 (GPIO interrupt + debounce software)

## Fase 2.6 — Drivers de actuadores

- [ ] Driver Relay con state machine
- [ ] Interlock: no abrir EV1 y EV2 simultáneamente (o reglas similares)
- [ ] Failsafe state al boot (todo OFF)
- [ ] Manual override desde botones físicos
- [ ] PWM si algún relé soporta dimming (no aplica, pero placeholder)

## Fase 2.7 — Almacenamiento persistente

- [ ] Wrapper NVS con namespaces: `wifi`, `mqtt`, `calibration`, `config`, `device`
- [ ] Schema versionado (campo `schema_version` en cada namespace)
- [ ] Migración automática al cambiar versión
- [ ] microSD: montaje FAT, escritura de logs CSV con rotación
- [ ] Buffer en RAM si SD no disponible, flush al recuperar

## Fase 2.8 — RTC y tiempo

- [ ] Driver DS3231 (lectura, escritura, alarmas)
- [ ] Sync inicial: si hay WiFi → SNTP, si no → mantener RTC
- [ ] Sync periódico cada 24h
- [ ] Timestamp en todos los logs y publicaciones MQTT

## Fase 2.9 — Watchdog y safety

- [ ] Habilitar Task Watchdog en cada tarea
- [ ] Watchdog hardware externo TPS3823 (kick por GPIO cada 1s)
- [ ] Panic handler: dump a microSD si es posible, reset
- [ ] Coredump partition + decodificación post-mortem

## Fase 2.10 — Arquitectura de tareas FreeRTOS

- [ ] Definir tareas: SensorTask (core 0), ActuatorTask (core 1), MqttTask (core 1), BleTask (core 0), AppLogicTask (core 0), HealthTask (core 1)
- [ ] Inter-task: queues (`xQueue`) y event groups
- [ ] Documentar prioridades y stack sizes
- [ ] Tests de stress (publicar a alta cadencia y verificar que no hay starvation)

## Fase 2.11 — WiFi connectivity

- [ ] STA mode con credenciales en NVS
- [ ] Reconexión exponencial con jitter
- [ ] Eventos a la app: `WIFI_CONNECTED`, `WIFI_DISCONNECTED`
- [ ] Modo AP fallback si configurable lo activa
- [ ] mDNS advertise (`acuaponico-{serial}.local`)

## Fase 2.12 — BLE provisioning

- [ ] NimBLE GATT server con servicios: Provisioning, Device Info
- [ ] Caracteristicas: WiFi SSID, WiFi PSK, Cloud URL, Device Cert request
- [ ] Encriptación BLE (LE Secure Connections, Just Works inicial, Numeric Comparison v2)
- [ ] Timeout BLE provisioning (10 min)
- [ ] Trigger provisioning: botón mantenido 5s o primera ejecución

## Fase 2.13 — MQTT

- [ ] esp-mqtt v5 con TLS
- [ ] Cargar cert + clave privada desde NVS
- [ ] Cargar CA del servidor
- [ ] Topics según `ARCHITECTURE.md` §5
- [ ] LWT configurado (`status` topic, retain)
- [ ] QoS por tipo de mensaje (telemetría QoS 1, eventos QoS 1, comandos QoS 2)
- [ ] Payload JSON (cJSON), schema versionado
- [ ] Buffer de outbox: si desconectado, cola en RAM (luego en flash si extensible)

## Fase 2.14 — OTA

- [ ] Trigger via comando MQTT
- [ ] Descarga HTTPS al partition inactivo
- [ ] Verificación SHA256 + firma RSA-2048 con clave pública embedded
- [ ] Marcado como pendiente, reset
- [ ] Confirmación tras N segundos sin reset (`esp_ota_mark_app_valid_cancel_rollback`)
- [ ] Reporte de progreso por MQTT
- [ ] Reporte de éxito/fallo al backend

## Fase 2.15 — Lógica de negocio (app)

- [ ] Configuración hot-reload desde MQTT (sin reflashear)
- [ ] Cadencias: temperatura agua, pH, TDS, ambiente, flujo, niveles
- [ ] Reglas de control: temperatura agua → calentador, niveles → bypass hidráulico
- [ ] Alertas: caudal=0 sostenido → bomba, filtro sucio si caudal<umbral
- [ ] Estados del sistema: STARTING, RUNNING, ERROR, MAINTENANCE
- [ ] Manual override desde app o botones

## Fase 2.16 — Calibración

- [ ] Wizard pH triple-punto (4.0, 7.0, 10.0)
- [ ] Wizard TDS un punto (1413 µS o equivalente)
- [ ] Almacenar curvas en NVS namespace `calibration`
- [ ] Reportar última calibración por MQTT
- [ ] Recordatorio (LED + topic event) cuando >30 días sin calibrar

## Fase 2.17 — Modo offline

- [ ] Funcionamiento sin red: lecturas locales, logs a microSD, alertas LED+buzzer
- [ ] Buzzer activo en alerta crítica (configurable mute por usuario)
- [ ] Sync automático al recuperar red (subir backlog de telemetría)

## Fase 2.18 — Seguridad

- [ ] Generar par de claves de firma OTA (RSA-2048) en máquina segura
- [ ] Embedded clave pública en firmware (no la privada)
- [ ] Habilitar Secure Boot v2 (irreversible — solo en build de producción, no debug)
- [ ] Habilitar Flash Encryption (irreversible)
- [ ] Script de aprovisionamiento de fábrica:
  - Genera serial + cert único
  - Programa eFuses
  - Inyecta cert + clave privada en NVS protegido
  - Firma con CA propia
  - Deja el device listo para vender
- [ ] Documentar proceso de fábrica

## Fase 2.19 — Tests

- [ ] Unit tests con Unity (framework de ESP-IDF) para:
  - Parser de mensajes MQTT
  - Curvas de calibración
  - Compensación de temperatura
  - State machine de relés
- [ ] HIL test bench: ESP32 + simulador de sensores → tests de regresión
- [ ] CI: build + tests on push

## Fase 2.20 — Documentación firmware

- [ ] Doxygen comments en headers públicos
- [ ] `firmware/README.md` con build instructions
- [ ] `firmware/HARDWARE.md` con pinout y datasheet links
- [ ] Mermaid diagram de tareas en docs/

**Entregable M2**: firmware compilable, todos los periféricos funcionando, MQTT/TLS conectado a Mosquitto local, OTA funcional, BLE provisioning funcional, tests pasando.

---

# M3 — Cloud Backend

> **Objetivo**: backend multi-tenant con auth, REST, WebSocket, ingesta MQTT y workers async, deployable en Hetzner con un comando.

## Fase 3.1 — Setup proyecto Python

- [ ] `backend/` con Poetry o uv
- [ ] Python 3.12+, FastAPI, SQLAlchemy 2.0, Alembic, Pydantic v2
- [ ] Estructura: `app/api/`, `app/models/`, `app/schemas/`, `app/services/`, `app/workers/`, `app/core/`, `tests/`
- [ ] `.env.example` con todas las vars
- [ ] Configuración con pydantic-settings
- [ ] Pre-commit: ruff, mypy strict, pytest

## Fase 3.2 — Base de datos

- [ ] Docker compose con PostgreSQL 16 + extensión TimescaleDB
- [ ] Modelos SQLAlchemy según `ARCHITECTURE.md` §6
- [ ] Migración inicial Alembic
- [ ] Seeds para desarrollo (org, user, device de prueba)
- [ ] Hypertables creadas vía migration con `create_hypertable`
- [ ] Continuous aggregates para 5min, 1h, 1d
- [ ] Retention policies configuradas

## Fase 3.3 — Autenticación y autorización

- [ ] Endpoint `/auth/register`, `/auth/login`, `/auth/refresh`, `/auth/logout`
- [ ] JWT con `python-jose` o `pyjwt`
- [ ] Argon2id para passwords
- [ ] Refresh token rotation con familia (detect reuse)
- [ ] Middleware de auth (dependency injection)
- [ ] RBAC: roles `org_admin`, `operator`, `viewer`
- [ ] Tests de auth (happy path + edge cases)

## Fase 3.4 — API REST: organizations, users, devices

- [ ] CRUD `/orgs/`
- [ ] CRUD `/users/`
- [ ] CRUD `/devices/`
- [ ] Pagination cursor-based
- [ ] Filtering + sorting
- [ ] Multi-tenancy: todas las queries scoped por `org_id` del usuario actual
- [ ] OpenAPI auto-generado
- [ ] Tests de cada endpoint

## Fase 3.5 — API REST: telemetría y series

- [ ] GET `/devices/{id}/telemetry` con range, sensor, agregación (raw/5min/1h/1d)
- [ ] WebSocket `/devices/{id}/live` para streaming en tiempo real
- [ ] Optimizar queries con índices y pre-agregados
- [ ] Test con dataset de 1M de filas

## Fase 3.6 — API REST: configuración y comandos

- [ ] PATCH `/devices/{id}/config` (reenvía por MQTT al device)
- [ ] POST `/devices/{id}/commands` (start, stop, calibrate)
- [ ] GET `/devices/{id}/state` (último estado conocido)
- [ ] POST `/devices/{id}/ota/trigger`

## Fase 3.7 — API REST: alertas

- [ ] CRUD `/alert-rules/`
- [ ] GET `/alerts/` con filtros
- [ ] PATCH `/alerts/{id}/ack`
- [ ] Notificación a usuarios al crear alerta (push, email, telegram)

## Fase 3.8 — Bridge MQTT

- [ ] Worker que suscribe al broker con credenciales backend
- [ ] Suscribe a `acuaponico/+/+/telemetry/#` y `acuaponico/+/+/event/#`
- [ ] Persiste telemetría en TimescaleDB
- [ ] Persiste eventos en `events` y dispara reglas de alerta
- [ ] Resilencia: backoff y reconexión automática

## Fase 3.9 — Workers async (Arq)

- [ ] Configuración Arq con Redis
- [ ] Tarea: enviar push notification (FCM)
- [ ] Tarea: enviar email (SMTP o SendGrid)
- [ ] Tarea: enviar Telegram (bot token)
- [ ] Tarea: agregar telemetría (si las continuous aggregates no bastan)
- [ ] Tarea: detectar anomalías (M5)

## Fase 3.10 — Provisioning de devices

- [ ] Endpoint `/devices/register` (autenticado por cert mTLS)
- [ ] Validación de cert contra CA propia
- [ ] Asignación a org según token de pairing del usuario
- [ ] Generación de serial humanamente legible
- [ ] Endpoint `/devices/firmware-releases` (lista + URL firmada)

## Fase 3.11 — Audit log

- [ ] Modelo `audit_log` con user_id, action, target, ts, ip, user_agent
- [ ] Middleware que loggea acciones críticas automáticamente
- [ ] Endpoint `/audit/` (solo org_admin)

## Fase 3.12 — Health, metrics, logging

- [ ] Endpoint `/healthz`, `/readyz`
- [ ] Métricas Prometheus en `/metrics`
- [ ] Logging estructurado JSON (structlog)
- [ ] Request ID propagation
- [ ] Sentry integration (opcional)

## Fase 3.13 — Rate limiting y seguridad

- [ ] slowapi o redis-based rate limit (60 req/min por user)
- [ ] CORS configurado correctamente
- [ ] CSP headers
- [ ] HSTS
- [ ] Tests de seguridad básicos (OWASP top 10 manual)

## Fase 3.14 — Tests e2e

- [ ] Pytest fixtures con DB de test
- [ ] Tests e2e: login → crear device → recibir telemetría → ver agregado → ack alerta
- [ ] Coverage objetivo >70%

**Entregable M3**: backend desplegable localmente con `docker compose up`, OpenAPI completo, todos los endpoints documentados y testeados, ingestion MQTT funcionando.

---

# M3.5 — Infraestructura cloud

> Va en paralelo a M3, pero merece sus propias tareas.

## Fase 3.15 — Docker

- [ ] Dockerfile multistage para backend (build + runtime mínimo)
- [ ] Dockerfile para worker
- [ ] docker-compose.yml dev: postgres, redis, mosquitto, backend, worker, mailhog
- [ ] docker-compose.prod.yml: con Caddy, sin volumes locales
- [ ] Healthchecks en todos los services

## Fase 3.16 — Hetzner deployment

- [ ] Crear cuenta y proyecto en Hetzner Cloud
- [ ] Provisionar CX22 (~5€/mes) en Falkenstein
- [ ] Configurar firewall (22 SSH, 80/443 HTTP, 8883 MQTTS)
- [ ] SSH keys, fail2ban, auto updates
- [ ] Instalar Docker + docker compose
- [ ] Setup script bash idempotente
- [ ] Terraform opcional para reproducibilidad

## Fase 3.17 — Caddy reverse proxy

- [ ] Caddyfile con `api.tudominio.com`, `app.tudominio.com`, `docs.tudominio.com`
- [ ] HTTPS automático
- [ ] Headers de seguridad
- [ ] Compresión gzip/zstd

## Fase 3.18 — Mosquitto en producción

- [ ] mosquitto.conf con TLS en 8883
- [ ] dynsec habilitado
- [ ] CA propia con `step-ca` o EasyRSA
- [ ] Persistence habilitado
- [ ] Logs a stderr → loki

## Fase 3.19 — Backups

- [ ] Script `pg_dump` cifrado con `age`
- [ ] Subida a Hetzner Object Storage
- [ ] Cron diario
- [ ] Retención 30 días con rotation
- [ ] Test de restore (¡hacerlo realmente, no asumir!)

## Fase 3.20 — Monitoring

- [ ] Prometheus + node_exporter
- [ ] Grafana con dashboards: backend RPS, latencia, DB, broker, devices online
- [ ] Loki para logs centralizados
- [ ] Alertmanager → Telegram/email para incidentes

## Fase 3.21 — CI/CD

- [ ] GitHub Actions: build images en push a main
- [ ] Push a GHCR con tag
- [ ] Deploy SSH a Hetzner: `docker compose pull && up -d`
- [ ] Smoke tests post-deploy
- [ ] Rollback strategy documentada

**Entregable M3.5**: VPS productivo con backend, broker, monitoring y backups funcionando, accesible vía HTTPS.

---

# M4 — Frontend

> **Objetivo**: web app funcional y app móvil con BLE provisioning, dashboard live, calibración guiada.

## Fase 4.1 — Web: setup Next.js

- [ ] `web/` con Next.js 15 + TypeScript estricto
- [ ] Tailwind v4
- [ ] shadcn/ui inicializado
- [ ] Estructura: `app/`, `components/`, `lib/`, `hooks/`, `services/`, `types/`
- [ ] Auth con NextAuth o custom (JWT del backend)
- [ ] React Query / TanStack Query para fetching
- [ ] Zod schemas compartidos con OpenAPI

## Fase 4.2 — Web: layout y navegación

- [ ] Layout principal con sidebar + header
- [ ] Navigation: Dashboard, Devices, Alerts, Calibration, Settings
- [ ] Dark mode con `next-themes`
- [ ] Responsive

## Fase 4.3 — Web: auth flow

- [ ] Página login
- [ ] Página register
- [ ] Página recuperar password
- [ ] Logout
- [ ] Refresh token automático (interceptor)

## Fase 4.4 — Web: dashboard

- [ ] Lista de devices con estado online/offline en tiempo real
- [ ] Card por device con: temp agua, pH, TDS, alertas activas
- [ ] Vista detalle de device:
  - Gráficas con `recharts` o `tremor` (last 24h, 7d, 30d, custom)
  - Estado actuadores en tiempo real (WebSocket)
  - Botones de control manual con confirmación
  - Histórico de eventos

## Fase 4.5 — Web: configuración

- [ ] Form para cadencias (validación: > 0)
- [ ] Form para umbrales (temp min/max, pH min/max, TDS rango)
- [ ] Form para canales de notificación (push, email, Telegram chat ID)
- [ ] Save → PATCH al backend → propaga al device por MQTT

## Fase 4.6 — Web: alertas

- [ ] Listado paginado con severity, code, ts, ack status
- [ ] Filtros: por device, por severity, por estado
- [ ] Detalle: gráfico de la sensor lectura alrededor del momento
- [ ] Botón ack
- [ ] Sugerencia IA de acción (M5)

## Fase 4.7 — Web: calibración

- [ ] Wizard paso a paso para pH (3 puntos)
- [ ] Wizard para TDS (1 punto)
- [ ] Visualización de curva resultante
- [ ] Histórico de calibraciones

## Fase 4.8 — Web: gestión de organización

- [ ] Listado usuarios de la org
- [ ] Invitar usuario por email
- [ ] Cambiar rol
- [ ] Eliminar (con audit)
- [ ] Datos de facturación (placeholder)

## Fase 4.9 — Web: tests

- [ ] Vitest para unit tests
- [ ] Playwright para e2e (login → dashboard → ack alerta)
- [ ] Lighthouse CI en GitHub Actions

## Fase 4.10 — Mobile: setup Expo

- [ ] `mobile/` con Expo SDK 52+ + TypeScript
- [ ] React Navigation con stack + tabs
- [ ] NativeWind (Tailwind para RN)
- [ ] Expo Router opcional
- [ ] EAS Build configurado para iOS y Android

## Fase 4.11 — Mobile: auth

- [ ] Login screen
- [ ] Register
- [ ] Persistencia de token con `expo-secure-store`
- [ ] Auto-login al abrir app

## Fase 4.12 — Mobile: BLE provisioning

- [ ] Permisos Android (BLUETOOTH_SCAN, BLUETOOTH_CONNECT, FINE_LOCATION)
- [ ] Permisos iOS (NSBluetoothAlwaysUsageDescription)
- [ ] Pantalla "Add device" → escanear QR
- [ ] Conectar BLE al device
- [ ] Form WiFi credentials
- [ ] Mostrar progreso
- [ ] Verificar que el device aparece en backend tras registro

## Fase 4.13 — Mobile: dashboard

- [ ] Lista de devices
- [ ] Pantalla de device con resumen
- [ ] Gráficas con `victory-native` o `react-native-svg-charts`
- [ ] Pull-to-refresh
- [ ] WebSocket live updates

## Fase 4.14 — Mobile: notificaciones push

- [ ] Configurar FCM (Android) y APNs (iOS) vía Expo Notifications
- [ ] Registrar token en backend
- [ ] Recibir alertas con deep link al detalle
- [ ] Acciones inline (ack)

## Fase 4.15 — Mobile: calibración

- [ ] Wizard equivalente al web
- [ ] Cámara para escanear QR del buffer comprado
- [ ] Cronómetro para esperar estabilización

## Fase 4.16 — Mobile: builds y distribución

- [ ] EAS Build internal distribution para tester (TestFlight + Play Console internal)
- [ ] OTA updates con EAS Update
- [ ] App icon, splash screen, store listings preparadas

**Entregable M4**: web app accesible en `app.tudominio.com`, app móvil instalable en iOS y Android (al menos versión beta), provisioning end-to-end funcional.

---

# M5 — Diferenciadores

> **Objetivo**: lo que hace al producto memorable.

## Fase 5.1 — Asistente IA con Claude

- [ ] Endpoint `/ai/chat` en backend
- [ ] System prompt cuidadosamente diseñado
- [ ] Tool definitions:
  - `get_sensor_history(device_id, sensor, range)`
  - `get_recent_alerts(device_id, limit)`
  - `get_device_config(device_id)`
  - `get_calibration_status(device_id)`
- [ ] Tool use loop con verificación de permisos (RLS por usuario)
- [ ] Prompt caching para system + tool definitions
- [ ] UI chat en web y móvil
- [ ] Sugerencias proactivas en alertas: "Pregunta a la IA por qué"

## Fase 5.2 — Detección de anomalías

- [ ] Worker que ejecuta Isolation Forest sobre últimos 30d por device
- [ ] Reentrenamiento semanal
- [ ] Score de anomalía por lectura
- [ ] Si score > umbral → alerta tipo `ANOMALY_DETECTED`
- [ ] Visualización de score en histograma

## Fase 5.3 — Recordatorios y mantenimiento predictivo

- [ ] Calibración mensual de pH/TDS
- [ ] Limpieza de filtro cuando caudal medio cae 20%
- [ ] Cambio de batería del RTC tras 5 años
- [ ] Notificaciones programadas

## Fase 5.4 — Exportación de datos

- [ ] Exportar telemetría a CSV/Parquet (rangos personalizados)
- [ ] API para integración externa (webhooks)
- [ ] Documentación de API pública con ejemplos

## Fase 5.5 — Configuración avanzada

- [ ] Reglas custom (if-this-then-that): "si pH > 8 durante 1h → encender bomba dosificadora"
- [ ] UI no-code para crear reglas

**Entregable M5**: asistente IA funcional con tool use, anomalías detectadas y notificadas, exportación de datos.

---

# M6 — Lanzamiento

> **Objetivo**: pasar de "proyecto técnico" a "algo que enseñar y vender".

## Fase 6.1 — Documentación final

- [ ] Manual de usuario en mkdocs (con screenshots)
- [ ] Manual de instalación física (con fotos del cableado)
- [ ] Datasheet del hardware (PDF de 2-4 páginas)
- [ ] Guía de calibración paso a paso
- [ ] Troubleshooting (errores comunes y soluciones)
- [ ] FAQ
- [ ] API docs en `/api/docs` con ejemplos
- [ ] Privacy policy + terms of service (templates legales adaptados)

## Fase 6.2 — Demo y assets

- [ ] Vídeo demo de 2-3 minutos: unboxing, provisioning, dashboard, IA, alerta
- [ ] Screenshots para README y landing
- [ ] GIFs animados de las features clave
- [ ] Diagrama de arquitectura final (Excalidraw exportable)
- [ ] Logo y branding básico

## Fase 6.3 — Landing page

- [ ] Subdominio `acuaponicoduino.com` o `.io`
- [ ] Single page con: hero, features, screenshots, video, pricing (placeholder), CTA "request demo"
- [ ] SEO básico (sitemap, meta tags, structured data)
- [ ] Formulario de contacto → email a ti

## Fase 6.4 — Calidad final

- [ ] Lighthouse scores > 90 en web
- [ ] Auditoría de accesibilidad (axe-core)
- [ ] Tests e2e completos pasando en CI
- [ ] Coverage backend > 70%
- [ ] Sin secretos en git history (verificar con `gitleaks`)
- [ ] Sin dependencias vulnerables (Dependabot al día)
- [ ] Lint zero warnings en todo

## Fase 6.5 — Open source / portfolio

- [ ] README principal con badges (build, license, version, docs)
- [ ] CHANGELOG actualizado
- [ ] Tag `v2.0.0` y release en GitHub con notas
- [ ] Post en LinkedIn explicando el proyecto
- [ ] Post en blog técnico personal (si tienes)
- [ ] Submission a Hacker News "Show HN"
- [ ] Submission a /r/aquaponics, /r/homeautomation, /r/esp32
- [ ] Solicitar feedback de comunidad

## Fase 6.6 — Validación con usuarios reales

- [ ] Encontrar 3-5 acuapónicos hobby con sistemas funcionando
- [ ] Ofrecerles unidad gratis a cambio de feedback brutal
- [ ] Instalación + onboarding asistido
- [ ] Recoger pain points durante 1-2 meses
- [ ] Iterar en v2.1 basándose en feedback real

## Fase 6.7 — Decisión de futuro

- [ ] ¿Continuar como portfolio? → mantener vivo, releases ocasionales
- [ ] ¿Convertir en producto? → constituir S.L., buscar inversión, plan v3 comercial
- [ ] ¿Open source completo? → documentar para contribuidores externos
- [ ] ¿Ambos? → modelo open-core (firmware abierto, cloud cerrado)

**Entregable M6**: producto presentable y validado por usuarios reales. Decisión informada sobre el siguiente paso.

---

# Riesgos transversales

| Riesgo | Probabilidad | Impacto | Mitigación |
|--------|--------------|---------|------------|
| PCB v0.1 con bug que requiere v0.2 | Alta | Medio | Aceptar y presupuestar 1 spin más en tiempo y dinero |
| Componentes obsoletos / no disponibles | Media | Medio | BOM con backup MPN, no usar partes exóticas |
| Burnout por scope ambicioso | **Alta** | **Alto** | Cumplir hitos pequeños, celebrar entregables, no comparar con productos comerciales |
| Subestimar tiempo de bringup HW | Alta | Medio | Multiplicar estimación inicial × 2 |
| Deriva de scope (feature creep) | Alta | Alto | "Out of scope" en arquitectura es ley. v3 existe |
| Falta de usuarios para validación | Media | Alto | Buscar comunidad acuapónica antes de tener producto |
| Cambios en APIs externas (Claude, Expo, etc.) | Baja | Bajo | Pin de versiones, lectura de changelogs |
| Lockout del propio device por error en Secure Boot | Baja | **Crítico** | Hacer flash encryption / secure boot solo cuando todo funciona, mantener al menos 1 unidad sin securizar |

---

# Definition of Done por tarea

Una tarea se marca `[x]` cuando:

1. ✅ Código mergeado en `main` (vía PR si trabajas con review)
2. ✅ Tests asociados pasan en CI
3. ✅ Documentación actualizada (en docs/ o README correspondiente)
4. ✅ Si aplica, demo en vídeo o GIF en el PR
5. ✅ Si introduce config nueva, `.env.example` actualizado
6. ✅ Sin TODO/FIXME sin issue asociado

---

# Cómo trabajar este roadmap

1. Cada fin de semana abre el `ROADMAP.md`
2. Elige una fase activa (no saltes; respeta dependencias)
3. Crea una rama `feat/0.1-setup-repo` (por ejemplo)
4. Cierra una o varias tareas concretas
5. PR a main, merge, marca `[x]`, commit con `docs(roadmap): ...`
6. Si aparecen tareas nuevas → añádelas con `[ ]` y commit
7. Cada cambio de milestone → tag y release notes

> Este documento **vive**. Cambia con el proyecto. Si una decisión se revisa, también se revisa aquí.
