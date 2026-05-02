# Hardware

Documentación del PCB, BOM, carcasa y bringup.

!!! info "Estado"
    Vacío. Se rellena en **M1** del [Roadmap](../ROADMAP.md).

## Contenido previsto

- Esquemáticos comentados (alimentación, MCU, analog frontend, digital I/O, relés, comms)
- BOM con MPN, distribuidor y precio a 1 / 10 / 100 / 1000 unidades
- Reglas de diseño y stackup PCB
- Manual de bringup paso a paso
- Errata por revisión PCB
- Diseño de carcasa y manual de ensamblaje

## Decisiones críticas tomadas

- **MCU**: ESP32-S3-WROOM-1 **N8R2** (PSRAM quad). Variantes octal **no compatibles** con el pinout.
- **Debug**: USB-Serial-JTAG nativo sobre USB-C. Sin header SWD/JTAG dedicado.
- **PCB**: 4 capas, 1.6mm, fabricación inicial en JLCPCB con SMT assembly.
- **Carcasa**: IP65 con cable glands y conectores M12 codificados.

Detalle completo en [`ARCHITECTURE §3 y §4`](../ARCHITECTURE.md#3-hardware-sensores-actuadores-asignación-de-pines).
