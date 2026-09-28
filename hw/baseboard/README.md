# MAGIE Baseboard

Carrier board for the AMD Kria K26 system-on-module (SOM) that connects high-density biosensor arrays directly to the FPGA fabric and streams their data to a host PC over 10 Gbit/s SFP+ Ethernet.

The baseboard is the hardware foundation of **MAGIE** (Multichannel Acquisition Gigabit Interface Engine), a retargetable FPGA-based acquisition platform for biosensor arrays. It was developed at the Integrated Systems Laboratory (IIS), ETH Zurich, primarily for the MICA 40k-pixel multimodal biochip. The same carrier has also been used for high-density surface EMG.

![MAGIE baseboard with K26 SOM and biochip fan-out board](assets/system_free.webp)
*MAGIE baseboard with the Kria K26 SOM and the fan-out board carrying the biochip.*

This repository contains the **hardware**: the KiCad sources, a jobset for reproducible manufacturing outputs, and documentation.

## Status


Results reported in the MAGIE master's thesis (Frey, 2026):

- Loss-free end-to-end capture to HDF5 at about 9.5 Gbit/s over the SFP+ link to a Linux workstation with a 10/100 GbE NIC.
- Extracellular recordings with the MICA biochip.
- HD-EMG recordings from an OT Bioelettronica Novecento+ probe, using the same carrier.
- The complete FPGA design uses less than 13 % of the K26 programmable logic.


## Board overview

| Item | Value |
|---|---|
| Size | 162.05 mm × 119.05 mm |
| Layers | 8 copper layers, 1.6 mm FR-4 |
| Impedance control | 50 Ω single-ended, 100 Ω differential (biochip-to-FPGA and high-speed lines) |
| Minimum track / clearance | 0.12 mm / 0.10 mm (outer layers), 0.15 mm to copper pours |
| Power input | 12 V DC, 5.5/2.1 mm barrel jack |
| SOM | AMD Kria K26 (2 × Samtec ADM6-60 connectors) |
| EDA tool | KiCad 10.0 |

### Stack-up

| Layer | Function | Copper | Dielectric below |
|---|---|---|---|
| F.Cu | Signal / components | 35 µm | 0.153 mm |
| In1 | GND | 30 µm | 0.250 mm |
| In2 | Signal | 30 µm | 0.138 mm |
| In3 | GND | 30 µm | 0.250 mm |
| In4 | Power | 30 µm | 0.138 mm |
| In5 | Signal | 30 µm | 0.250 mm |
| In6 | GND | 30 µm | 0.153 mm |
| B.Cu | Signal / components | 35 µm | – |


### Functional blocks

**Power distribution.** The 12 V input feeds a MAX20008 buck converter that generates the 5 V rails for the SOM and the peripherals. MPM3632C step-down modules supply:

- the processing-system (PS) I/O domain: 3.3 V and 1.8 V;
- the programmable-logic (PL) I/O banks and peripherals: 3.3 V, 2.5 V, 1.8 V and 1.2 V.

An INA3221 monitors currents and voltages over I2C, and a MAX6710 supervisor monitors the supply rails. The PS and PL domains are enabled either by the SOM or by applying 1.8 V to the corresponding enable pads.

**Biochip supplies.** Low-noise TPS7A94 LDOs generate four biochip rails:

- DVDD1 1.15 V and DVDD2 2.5 V (digital);
- AVDD1 1.1 V (analog);
- AVDD2, adjustable from 1.5 V to 2.5 V through an MCP4023 digital potentiometer controlled by the FPGA.


**Bias voltages.** Sixteen low-noise bias outputs are generated from REF35 references, buffered by op-amps and set by AD5144 quad digital potentiometers under FPGA control. Alternatively, you can set them with manual trimmers or with 0603 resistor dividers on the provided pads.

**Clocks.** The board has these oscillators:

- 25 MHz for the FPGA core clock, via an MMCM;
- 125 MHz LVDS for 1 Gbit/s Ethernet;
- 156.25 MHz LVDS for the 10 Gbit/s SFP+ transceiver;
- a separate 25 MHz clock routed to the biosensor connector;
- a third 25 MHz clock for the 1 Gbit/s Ethernet PHY.

**Biosensor connector.** A 240-pin Samtec SEAF (6 × 40, 1.27 mm) on the bottom side carries the HP and HD I/O banks, the 16 bias voltages, the biochip supplies and the 25 MHz frontend clock. The biochip sits on a separate daughter board (about 4.5 cm × 4.5 cm, 160-pin 0.5 mm edge connector) that connects through a small interposer. This decoupling lets the carrier serve other chip packages or biosensor arrays.

## Connectors and controls

| Ref. | Function |
|---|---|
| J9 | 12 V DC input (barrel jack) |
| SW4 | Main power switch (enables the 5 V converter) |
| J3, J4 | Kria K26 SOM connectors |
| J5 | SFP+ cage, 10 Gbit/s Ethernet (GTH transceiver) |
| J6 | RJ45, 1 Gbit/s Ethernet (DP83867 PHY) |
| J7 | USB-C, UART console (FT234XD) |
| J2 | JTAG, 14-pin 2 mm (for example Digilent JTAG-HS2) |
| J8 | microSD card (boot medium) |
| SW2 | Boot-mode DIP switch (MODE0–MODE3) |
| SW1 / SW3 | Reset / firmware-update enable (FWUEN) buttons |
| J18 | Biosensor fan-out connector (bottom side) |
| J10 / J11 | PS / PL supply rail bridge headers (5 V, 3.3 V, 1.8 V / 3.3 V, 2.5 V, 1.2 V) |
| J19 | Biochip supply bridge header (DVDD1, DVDD2, AVDD1, AVDD2) |
| J12 | Bias digital potentiometer bus and bias test points |
| J13, J14 | Biochip analog outputs (buffered) |
| J16 | Biochip analog inputs |
| J15, J17 | Spare HP / HD I/O |
| J1 | Fan header |
| TP1–TP8 | Power-good, power-on reset, I2C and bank-enable test points |

<!-- ## Getting started -->

<!-- 1. Mount the K26 SOM. Mount the fan-out board and biochip on J18 if you need them.
2. Bridge the rail headers **J10**, **J11** and **J19**. The supply rails are only connected to their loads when these headers are bridged. This lets you bring up and measure each rail separately.
3. Set the boot mode with **SW2** (MODE0–MODE3). The encoding for SD-card boot is in the Kria K26 SOM data sheet (DS987).
4. Insert a microSD card with the MAGIE Linux image into **J8**.
5. Connect USB-C (**J7**) for the UART console and, if needed, a JTAG cable to **J2**.
6. Connect 12 V to **J9** and switch on with **SW4**.
7. Connect the SFP+ port (**J5**) to the host, using a direct-attach cable or a fiber transceiver. -->

## Repository layout

```text
baseboard/
├── MAGIE_baseboard.kicad_pro   # KiCad project (root)
├── MAGIE_baseboard.kicad_sch   # top level: reset, LEDs, fan, frontend clock, mounting
├── MAGIE_baseboard.kicad_pcb   # PCB layout
├── MAGIE_baseboard.kicad_dru   # custom design rules
├── hw/
│   ├── fpga_conn.kicad_sch     # K26 SOM connectors, boot mode, JTAG, reference clocks
│   ├── pcb_conn.kicad_sch      # SFP+, 1 GbE PHY + RJ45, USB-UART, microSD
│   ├── power.kicad_sch         # 12 V input, power switch, SOM/PS/PL supplies, monitoring
│   ├── bio_pwr.kicad_sch       # biochip supplies and bias generation
│   ├── bio_io.kicad_sch        # biosensor fan-out connector, analog buffers, I/O headers
│   ├── jobset.kicad_jobset     # manufacturing-output jobs
│   └── 3d_vendor/              # vendor 3D models (download locally, not tracked)
├── production/                 # generated manufacturing package (per release)
├── assets/                     # images
└── CHANGELOG.md                # board revision history
```

## Opening the design

Open `MAGIE_baseboard.kicad_pro` with **KiCad 10.0**. The schematic and PCB contain embedded copies of all symbols and footprints, so you can view, check and fabricate the design without any extra libraries.

 **Library dependency.** The symbols and custom footprints come from the [`dz_parts`](https://gitlab.ethz.ch/kicad/dz_parts) KiCad library (with symbols from its `#dzdb` database library). See the [top-level README](../../README.md#opening-the-designs) for the setup. Without it, KiCad reports missing-library warnings in ERC/DRC, and you cannot update symbols or footprints from their libraries.



## Manufacturing outputs

To regenerate the complete manufacturing package from the sources, run this from `hw/baseboard/`:

```bash
kicad-cli jobset run -f hw/jobset.kicad_jobset MAGIE_baseboard.kicad_pro
```

It writes the following to `production/MAGIE_baseboard/` and `production/MAGIE_baseboard_fabrication.zip`:

- Gerber X2 files for all 8 copper layers, plus mask, paste, silkscreen, fabrication and outline layers;
- the Gerber job file, which contains the stack-up;
- Excellon drill files and drill map;
- the BOM (`assembly/bom.csv`, with manufacturer, MPN and LCSC numbers);
- pick-and-place data (`assembly/position-all-pos.pos`);
- the schematic PDF.

Refill all zones in KiCad before generating outputs for production. Release packages are attached to tagged releases.

## Contributors

- Federico Villani: V2 board design, review and project supervision
- Pascal Wesemann: original board design (V1), released V1 board under permissive license at https://gitlab.ethz.ch/ltl/theses/biochip/biochip_pcb
- Tim Frey: MAGIE platform integration and validation, silkscreen

Supervision: Dr. Hangxing Liu, Federico Villani, Prof. Dr. Luca Benini (Integrated Systems Laboratory, ETH Zurich).

## Citation

If you use this board, please cite it using [`CITATION.cff`](../../CITATION.cff) or:

**TBD**

The MICA biochip is described in H. Liu et al., "A 40k-Pixel Multimodal Biophysiology Monitoring Platform With 10k Concurrent Electrophysiology Channels and a Mixer-Embedded Impedance Sensor," *IEEE Journal of Solid-State Circuits*, 2026.

## License

See the [top-level README](../../README.md#license).
| Content | License |
|---|---|
| Hardware design files | [Solderpad Hardware License v0.51](LICENSES/SHL-0.51.txt) (`SHL-0.51`) |
| Documentation and images  | [Creative Commons Attribution 4.0](LICENSES/CC-BY-4.0.txt) (`CC-BY-4.0`) |
