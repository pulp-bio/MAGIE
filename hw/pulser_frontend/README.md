# MAGIE Pulser Frontend

64-channel ultrasound transmit/receive frontend. The board plugs into an FPGA baseboard through a PCIe x16 card edge. It drives an ultrasound transducer array with ±100 V pulses and was designed for four 16-channel receive AFEs with JESD204B outputs.

For bring-up, see the [frontend tester](../frontend_tester) and the [pulser interface tester](../pulser_interface_tester).


## Status

| Revision | Date | Status |
|---|---|---|
| **1.1** | 2026-09 | **Current sources.**|

## Board overview

| Item | Value |
|---|---|
| Size | 220.00 mm × 120.05 mm |
| Layers | 8 copper layers, 1.578 mm; RO4350B outer cores, FR-4 inner layers; ENIG finish |
| Channels | 64 transmit (4 × STHV1600) and 64 receive (4 × AFE58JD48, not included) |
| Transmit | ±100 V, 16 channels per pulser |
| Receive data | JESD204B to the FPGA baseboard |
| Minimum track / clearance | 0.10 mm / 0.10 mm (0.184–0.19 mm controlled width for JESD204B on outer layers) |
| Minimum via drill | 0.15 mm |
| Power input | 12 V, 6-pin Mini-Fit Jr. (PCIe power) connector J1 |
| Host interface | PCIe x16 card edge (J4) to the FPGA baseboard |
| Transducer interface | PCIe x8 card edge (J19) |
| EDA tool | KiCad 10.0 |

### Stack-up

| Layer | Function | Copper | Dielectric below |
|---|---|---|---|
| F.Cu | Signal / components, JESD204B | 35 µm | 0.168 mm RO4350B core |
| In1 | GND | 35 µm | 0.200 mm FR-4 prepreg |
| In2 | Power, GND fill | 35 µm | 0.161 mm FR-4 core |
| In3 | Power, GND fill | 35 µm | 0.220 mm FR-4 prepreg |
| In4 | GND | 35 µm | 0.161 mm FR-4 core |
| In5 | Signal, GND fill | 35 µm | 0.200 mm FR-4 prepreg |
| In6 | GND | 35 µm | 0.168 mm RO4350B core |
| B.Cu | Signal / components, JESD204B | 35 µm | – |

### Functional blocks

**Channel groups.** The board has four identical channel groups (`hw/chan.kicad_sch`). Each group contains:

- one STMicroelectronics STHV1600 16-channel ±100 V pulser (`UP1`–`UP4`);
- the footprint area for one AFE58JD48 16-channel receive AFE (`UA1`–`UA4`) (not present in schematic and layout);
- the transducer input matching;
- a high-voltage header (`JP1_1`–`JP1_4`) for the positive and negative pulser supplies (HVP0/1, HVM0/1) and an external control line;
- a TCA9534 I/O expander for the AFE and pulser hardware control pins.

**Power.** The 12 V input goes through a BTS5030 high-side switch and an INA233 current and power monitor. Three TPSM64406 dual buck modules generate 3.7 V and 2.2 V (U10), 3.3 V and 1.8 V (U12), and the 1.15 V AFE digital rail (U11, both phases on one output, 6 A). A TPS63710 (U13) generates the −3.3 V pulser rail. Low-noise TPS7A9601 LDOs generate the AFE analog rails (3.3 V and 1.8 V), and MAX38902A LDOs generate the pulser rails (3.3 V and 1.8 V). The AFE digital 1.8 V rail comes directly from the 1.8 V buck. Jumpers JP1–JP6 connect the buck outputs to their loads (the LDOs, the AFE digital rails and the 3.3 V rail): fit all six for normal operation.

An LM3880 sequencer powers the rails up in stages and down in reverse order. It starts once the 3.7 V, 2.2 V and 3.3 V bucks report power good and `AFE_PLS_EN` is high. It then enables the 1.8 V rails (`EN_A_s2`), the AFE 1.15 V and 3.3 V rails (`EN_A_s3`) and the pulser rails (`EN_P_s4`), 2 ms apart. An always-on 3.3 V auxiliary rail supplies the monitoring and control logic. The host can pull the global enable low for a complete shutdown.

**Clocks and synchronisation.** The FPGA baseboard supplies the clocks. Two LMK00304 1:4 buffers distribute the AFE ADC clock and SYSREF to the four AFEs. Further 1:4 buffers distribute `~SYNC` (SN65LVDS104) and the transmit trigger `TX_TRIG` (LMK1C1104). All clock signals are length-matched from the connector to the buffers and from the buffers to the AFEs.

**Control.** The host controls the board over SMBus/I2C (I/O expanders, power monitors, clock enables, board EEPROM) and SPI (AFE configuration, one chip select per AFE). The pulsers share LVDS clock, data and trigger lines. Two board-ID pins, set on the baseboard, let you use up to four frontends on one host. An M24C02 EEPROM stores board information and can be write-protected with a solder resistor.

**TGC expansion.** Headers J5–J8 connect an optional time-gain-control (TGC) expansion board. See the TI TIDA-01427 reference design.

### SMBus addresses

The addresses are 7-bit. On V1 boards, four devices answer at other addresses because of erratum 1.5, and the EEPROM strap is different.

| Device | Ref. | Rev 1.1 | V1 |
|---|---|---|---|
| I/O expander, channel group 0 | UA_SMB20 | 0x20 | 0x20 |
| I/O expander, channel group 1 | UA_SMB1 | 0x21 | 0x24 |
| I/O expander, channel group 2 | UA_SMB2 | 0x22 | 0x22 |
| I/O expander, channel group 3 | UA_SMB3 | 0x23 | 0x26 |
| I/O expander, power monitor (`AFE_PLS_EN`, power-good signals) | U_SMB24 | 0x24 | 0x21 |
| I/O expander, clock enables | U_SMB25 | 0x25 | 0x25 |
| I/O expander, TGC | U_SMB26 | 0x26 | 0x23 |
| INA233 current and power monitor | U_SMB40 | 0x40 | 0x40 (see [known issues](#known-issues)) |
| M24C02 EEPROM | U_SMB50 | 0x51 | 0x50 (see [known issues](#known-issues)) |

## Connectors and headers

| Ref. | Function |
|---|---|
| J1 | 12 V power input (6-pin Mini-Fit Jr., PCIe power) |
| J4 | PCIe x16 card edge to the FPGA baseboard (JESD204B, SPI, SMBus, clocks, pulser control, board ID) |
| J19 | Transducer card edge (PCIe x8): 64 channels, plus 12 V and the pulser low-voltage rails |
| JP1_1–JP1_4 | Pulser high-voltage supply header, one per channel group |
| JP1–JP6 | Rail jumpers between the buck outputs and their loads. Fit all six. |
| J2 | AFE SPI debug header |
| J9, J15 | SMBus/I2C, IRQ and enable debug headers |
| J10 | SYSREF, ~SYNC and spare transceiver lane test header |
| J12 | Pulser LVDS control test header |
| J17 | Spare transceiver lane header |
| J5–J8 | TGC expansion board headers |
| J13, J14, J16 | Auxiliary power, −3.3 V and 12 V shunt monitoring headers |
| J3, J18, J20–J22 | Rail and pulser data-out test headers |

> **High voltage.** The pulser supplies reach ±100 V. Switch off the HV supplies before you touch the board or the transducer. The host connector has no ESD protection: take ESD precautions when you handle the board.

## AFE58JD48 removed

> **Warning.** The schematic symbol and the footprint of the four TI AFE58JD48 receive AFEs (`UA1`–`UA4`) have been removed from this release. 

Each AFE site is marked by a named rule area on F.Cu, `NDA_AFE_UA1` to `NDA_AFE_UA4`. 


## Repository layout

```text
pulser_frontend/
├── MAGIE_pulser_frontend.kicad_pro   # KiCad project (root)
├── MAGIE_pulser_frontend.kicad_sch   # top level: channel groups, connectors, clocks, power
├── MAGIE_pulser_frontend.kicad_pcb   # PCB layout
├── MAGIE_pulser_frontend.kicad_dru   # custom design rules
├── CHANGELOG.md                      # revision history
└── hw/
    ├── chan.kicad_sch        # one channel group (instantiated 4×)
    ├── pulser.kicad_sch      # STHV1600 pulser and transducer matching
    ├── afe.kicad_sch         # AFE58JD48 support circuitry (AFE removed, see above)
    ├── afe_ctrl.kicad_sch    # AFE control, SMBus level translation, TGC expansion
    ├── clk.kicad_sch         # ADC clock, SYSREF, ~SYNC and TX_TRIG distribution
    ├── con_mb.kicad_sch      # PCIe x16 card edge to the FPGA baseboard, EEPROM, board ID
    ├── con_xdcr.kicad_sch    # transducer connector
    ├── pwr.kicad_sch         # 12 V input, monitoring, power sequencing and rail jumpers
    ├── pwr_buck.kicad_sch    # DC/DC buck converters
    ├── pwr_afe_pls.kicad_sch # AFE and pulser LDOs
    └── jobset.kicad_jobset   # manufacturing-output jobs
```

## Opening the design

Open `MAGIE_pulser_frontend.kicad_pro` with **KiCad 10.0**. The schematic and PCB contain embedded copies of all symbols and footprints, so you can view and check the design without extra libraries.

> **Library dependency.** The symbols and custom footprints come from the [`dz_parts`](https://gitlab.ethz.ch/kicad/dz_parts) KiCad library (with symbols from its `#dzdb` database library). See the [top-level README](../../README.md#opening-the-designs) for the setup. Without it, KiCad reports missing-library warnings in ERC/DRC, and you cannot update symbols or footprints from their libraries.

## Manufacturing outputs

To generate the manufacturing outputs, run this from this folder:

```bash
kicad-cli jobset run -f hw/jobset.kicad_jobset MAGIE_pulser_frontend.kicad_pro
```

The outputs are written to `generated/`. Refill all zones in KiCad before generating outputs for production.



## Contributors

- Federico Villani: System Architecture, Schematic and Layout design

Supervision: Dr. Andrea Cossettini, Dr. Christian Vogt, Prof. Dr. Luca Benini (Integrated Systems Laboratory, ETH Zurich).

## License

See the [top-level README](../../README.md#license).
| Content | License |
|---|---|
| Hardware design files | [Solderpad Hardware License v0.51](LICENSES/SHL-0.51.txt) (`SHL-0.51`) |
| Documentation and images  | [Creative Commons Attribution 4.0](LICENSES/CC-BY-4.0.txt) (`CC-BY-4.0`) |
