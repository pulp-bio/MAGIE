# Baseboard for K26 SOM and Biochip

This Baseboard PCB is able to connect the Biochip directly to the FPGA-fabric. It has a 10 Gbit/s SFP+ interface to transfer data to a host PC. The PCB is supplied with 12 V by an external power supply. The board is able to provide power to the K26 SOM, Biochip and all peripherals on the PCB.

The design of this PCB is open source, licensed under the Solderpad Hardware License \- Version 0.51 (SHL-0.51).

![Baseboard with K26 and Biochip connected](/assets/system_free.png) Baseboard PCB with K26 and Fanout-Board with Biochip connected

## Power Supplies
The board is supplied with 12 V through a barrel connector by an external power supply. The PS- and PL-domain supplies are enabled by the FPGA or a 1.8 V potential supplied to the corresponding pads. To supply the board with the voltage rails, the pin headers need to be bridged.

## Kria K26 SOM
The K26 can be interfaced by the UART (USB-C) or JTAG (Digilent HS2 programming cable) interface. The SOM should be able to boot from the micro-SD-card.

## Biochip
The biochip is connected via a fanout-board to the SEAM connector on the bottom of the PCB.

The chip is configured by the FPGA. It is supplied by LDOs. The variable voltage rail AVDD2 (1.5 - 2.5 V) can be configured through a digital potentiometer by the FPGA.

The bias voltages are configured through digital potis by the FPGA or manual potis with a screwdriver. 0603 resistors can be placed on pads to set voltage dividers manually.

The analog outputs of the biochip are available on pin headers.

## Errata
So far, no mayor hardware bugs have been found yet. Minor problems to improve in future revisions are listed below:
- Fan Power Cable:
The power cable of the fan provided with the Kria KR260 Robotics Starter Kit is too short to connect to the fan-header on the PCB.

- Inductor L1 (5 V power supply output):
Before operational use, the inductor L1 at the output of the 5 V supply needs to be replaced with one rated for > 8 A.

- SFP+ connector:
The SFP+ connector does not engage properly with a plugged in transceiver. Data transfer should still be possible.
