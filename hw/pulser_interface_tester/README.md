# MAGIE Pulser Interface Tester

Break-out board for testing the transmit channels of the [MAGIE pulser frontend](../pulser_frontend) without a transducer. It plugs onto the frontend's PCIe x8 transducer card edge (J19) and brings all 64 pulser channels out to 2.54 mm headers. Four channels can also be routed to SMA connectors for oscilloscope measurements.


## Status

| Revision | Date | Status |
|---|---|---|
| **V1** | 2026-03 | Manufactured. **Current sources.** |

## Board overview

| Item | Value |
|---|---|
| Size | 78.94 mm × 45.50 mm |
| Layers | 6 copper layers, 1.584 mm FR-4 (JLCPCB JLC06161H-7628, the same stack-up as the [frontend tester](../frontend_tester/README.md#stack-up)) |
| Minimum track / clearance | 0.20 mm / 0.15 mm |
| Minimum via drill | 0.30 mm |
| Frontend interface | PCIe x8 slot, right angle (J19, Amphenol 10146070-112Y0LF) |
| EDA tool | KiCad 10.0 |

## Connectors and options

| Ref. | Function |
|---|---|
| J19 | PCIe x8 slot for the frontend transducer card edge |
| J16 | Channels XDCR0–XDCR15 |
| J15 | Channels XDCR16–XDCR31 |
| J18 | Channels XDCR32–XDCR47 |
| J20 | Channels XDCR48–XDCR63 |
| J1, J2, J3, J4 | SMA outputs for XDCR0, XDCR1, XDCR16 and XDCR17 |

Channels XDCR0, XDCR1, XDCR16 and XDCR17 each have two 0 Ω resistors. Fit the one that goes to the SMA connector (R1, R4, R5, R8) or the one that goes to the header (R2, R3, R6, R7), not both.

> **High voltage.** The pulser outputs reach ±100 V on the headers and SMA connectors. Switch off the frontend's high-voltage supplies before you connect or disconnect probes, and use probes and loads rated for the pulse voltage.

## Repository layout

```text
pulser_interface_tester/
├── MAGIE_pulser_interface_tester.kicad_pro   # KiCad project (root)
├── MAGIE_pulser_interface_tester.kicad_sch   # schematic
├── MAGIE_pulser_interface_tester.kicad_pcb   # PCB layout
├── MAGIE_pulser_interface_tester.kicad_dru   # custom design rules
├── CHANGELOG.md                              # revision history
├── fabrication-toolkit-options.json          # KiCad Fabrication Toolkit settings
└── hw/
    └── jobset.kicad_jobset                   # manufacturing-output jobs
```

## Opening the design

Open `MAGIE_pulser_interface_tester.kicad_pro` with **KiCad 10.0**. The schematic and PCB contain embedded copies of all symbols and footprints.


## Manufacturing outputs

To generate the manufacturing outputs, run this from this folder:

```bash
kicad-cli jobset run -f hw/jobset.kicad_jobset MAGIE_pulser_interface_tester.kicad_pro
```

The outputs are written to `generated/`.

## Contributors

- Federico Villani: board design

## License

| Content | License |
|---|---|
| Hardware design files | [Solderpad Hardware License v0.51](LICENSES/SHL-0.51.txt) (`SHL-0.51`) |
| Documentation and images  | [Creative Commons Attribution 4.0](LICENSES/CC-BY-4.0.txt) (`CC-BY-4.0`) |
