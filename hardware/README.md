# hardware/

Diseño del PCB (KiCad 8) y de la carcasa.

## Estado

Vacío. Se llenará a partir de **M1** del [ROADMAP](../docs/ROADMAP.md).

## Estructura prevista

```
hardware/
├── kicad/
│   └── acuaponicoduino-main/
│       ├── acuaponicoduino-main.kicad_pro
│       ├── acuaponicoduino-main.kicad_sch     # top sheet
│       ├── power.kicad_sch
│       ├── mcu.kicad_sch
│       ├── analog.kicad_sch
│       ├── digital_in.kicad_sch
│       ├── relay_out.kicad_sch
│       ├── comms.kicad_sch
│       ├── acuaponicoduino-main.kicad_pcb
│       └── lib/                                # símbolos y footprints custom
├── bom/
│   ├── BOM.csv                                 # con MPN, distributor PN, precio
│   └── BOM-DNP.csv                             # componentes no poblados
├── enclosure/
│   ├── enclosure.FCStd                         # FreeCAD source (LFS)
│   └── exports/                                # STL, STEP (LFS)
├── fab/                                        # outputs gerber, pick&place — generado, gitignored
└── ERRATA.md                                   # bugs por revisión PCB
```

## Reglas de diseño

- **4 capas**: Signal / GND / PWR / Signal
- **Stackup estándar 1.6mm** JLCPCB
- **Track widths por clase de net**: power 8mil, signal 6mil, USB diff pair 5mil
- **Clearance**: ≥ 6mil (low-cost JLC), ≥ 3mm entre tracks AC y lógica
- **DRC limpio antes de cada commit que toque PCB**

## Outputs generados

`fab/`, `gerbers/`, `*-bak/` están en `.gitignore`. Los regeneras desde el `.kicad_pcb` con `kicad-cli pcb export gerbers`. Los ficheros pesados (3D, STEP, fotos) van por **Git LFS** — ver `.gitattributes`.

## Conectores M12 codificados

| Código | Tipo de sensor |
|--------|----------------|
| A      | sondas analógicas (pH, TDS) |
| B      | temperatura (DS18B20) |
| C      | caudal (FS400A) |
| D      | nivel (FS-IR02) |
