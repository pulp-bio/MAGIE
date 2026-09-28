# MAGIE Frontend Tester

Adapter board for bring-up of the [MAGIE pulser frontend](../pulser_frontend) on a commercial FPGA development kit. The frontend plugs into a PCIe x16 slot on this board. The tester routes the frontend's JESD204B lanes, clocks and control signals to an FMC connector for the iWave iW-RainboW-G35D Zynq UltraScale+ ZU19EG development kit.

## Status

| Revision | Date | Status |
|---|---|---|
| **V1** | 2025-12 | Manufactured. **Current sources.** |

## Board overview

| Item | Value |
|---|---|
| Size | 157.64 mm × 45.69 mm |
| Layers | 6 copper layers, 1.584 mm FR-4 (JLCPCB JLC06161H-7628, see [Stack-up](#stack-up)) |
| Minimum track / clearance | 0.10 mm / 0.125 mm |
| Minimum via drill | 0.15 mm |
| Frontend interface | PCIe x16 slot, right angle (J5, Amphenol 10151422-0222120LF) |
| FPGA interface | FMC connector (J19, Samtec ASP-134602-01) |
| Power | From the development kit through the FMC connector |
| EDA tool | KiCad 10.0 |

The development kit generates all tester voltages. The frontend still needs its own 12 V supply on its J1 connector: the +12 V and +3.3 V pins of the PCIe slot are not connected. Only the 3.3 V auxiliary rail goes from the FMC connector to the frontend, through jumper J18.

> **FMC signal direction.** FMC names are seen from the carrier. For example, module-to-carrier (M2C) lanes carry data from the frontend to the ZU19EG SoC, and carrier-to-module (C2M) lanes carry data from the SoC to the frontend.

### Stack-up

JLCPCB 6-layer stack-up JLC06161H-7628, 1.584 mm including solder mask.

| Layer | Function | Copper | Dielectric below |
|---|---|---|---|
| F.Cu | Signal / components | 35 µm | 0.2104 mm prepreg (7628) |
| In1 | GND | 15.2 µm | 0.40 mm core |
| In2 | Signal | 15.2 µm | 0.2028 mm prepreg (7628) |
| In3 | GND | 15.2 µm | 0.40 mm core |
| In4 | Power | 15.2 µm | 0.2104 mm prepreg (7628) |
| B.Cu | Signal / components | 35 µm | – |

Controlled-impedance track widths for this stack-up (JLCPCB impedance calculator):

| Impedance | Layer | Reference planes | Width / gap |
|---|---|---|---|
| 50 Ω single-ended | F.Cu | In1 | 0.349 mm |
| 100 Ω differential | F.Cu | In1 | 0.177 mm / 0.15 mm |
| 100 Ω differential | In2 | In1 and In3 | 0.130 mm / 0.15 mm |

The `DIFF` net class (all nets ending in `P` or `N`) uses the 0.177 mm / 0.15 mm outer-layer pair.

## Connectors and headers

| Ref. | Function |
|---|---|
| J5 | PCIe x16 slot for the pulser frontend card edge |
| J19 | FMC connector to the iWave development kit |
| J_ADCCLK_P/N_1, J_SYSREF_P/N_1 | SMA inputs for an external AFE ADC clock and SYSREF to the frontend |
| J_FPGA_ADCCLK_P/N_1, J_FPGA_SYSREF_P/N_1 | SMA inputs for the FPGA-side ADC clock and SYSREF |
| J4, J6, J13, J14, J15 | Clock routing headers: SYSREF, ADC clock, ~SYNC and FMC GBT reference clocks |
| J2, J7, J9 | AFE SPI, chip selects, TGC SPI, trigger, enable and IRQ break-out to FMC LA pins |
| J8 | SMBus/I2C routing and IRQ |
| J10 | Pulser LVDS control signals and pulser data out |
| J11 | Spare FMC LA pairs |
| J1, J12 | AFE fast power-down and pulser serial enable jumpers (1.8 V) |
| J16, J17, J18 | FMC power-good, VADJ and 3.3 V auxiliary jumpers |
| J3, SW1 | System reset header and push button |

Some pads of J19 (E28, E29, E30, F28, F29, F30) are intentionally left out on the bottom layer and are not connected. The schematic-parity check in DRC lists them.

## Known issues

- The polarity of the frontend's `CH1_8` JESD204B lanes is inverted because of routing on the frontend. Correct it in the FPGA transceiver configuration.

## Repository layout

```text
frontend_tester/
├── MAGIE_frontend_tester.kicad_pro   # KiCad project (root)
├── MAGIE_frontend_tester.kicad_sch   # top level: PCIe slot, headers, SMA clocks
├── MAGIE_frontend_tester.kicad_pcb   # PCB layout
├── MAGIE_frontend_tester.kicad_dru   # custom design rules
├── CHANGELOG.md                      # revision history
├── hw/
│   ├── fmc_iwave.kicad_sch           # FMC connector to the iWave development kit
│   └── jobset.kicad_jobset           # manufacturing-output jobs
└── 3d_vendor/                        # optional 3D models (download locally, not tracked)
```

## Opening the design

Open `MAGIE_frontend_tester.kicad_pro` with **KiCad 10.0**. The schematic and PCB contain embedded copies of all symbols and footprints.

The 3D models of the iWave development kit and of the pulser frontend are not included. See [`3d_vendor/README.md`](3d_vendor/README.md).

## Manufacturing outputs

To generate the manufacturing outputs, run this from this folder:

```bash
kicad-cli jobset run -f hw/jobset.kicad_jobset MAGIE_frontend_tester.kicad_pro
```

The outputs are written to `generated/`.

## Contributors

- Federico Villani: board design

## License

See the [top-level README](../../README.md#license).
| Content | License |
|---|---|
| Hardware design files | [Solderpad Hardware License v0.51](LICENSES/SHL-0.51.txt) (`SHL-0.51`) |
| Documentation and images  | [Creative Commons Attribution 4.0](LICENSES/CC-BY-4.0.txt) (`CC-BY-4.0`) |

