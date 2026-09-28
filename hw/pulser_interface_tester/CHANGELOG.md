# Changelog

All notable changes to the MAGIE pulser interface tester hardware are
documented here. The silkscreen of the manufactured V1 boards reads "LTL XDCR
channel tester".

## [V1-open] - 2026-09-27

Preparation for the open-source release. The circuit and routing are unchanged.

- Renamed the KiCad project from `LTL_pulser_interface_tester` to
  `MAGIE_pulser_interface_tester`. The title blocks read "MAGIE Pulser
  Interface Tester", rev V1, 2026-09-27, IIS.
- Silkscreen: the board name on F.Mask reads "MAGIE Pulser Interface Tester
  V1" (on two lines, in the place of the old name). It is set in Barlow Bold
  instead of Times New Roman, and the font is embedded in the PCB file.
- Set the project and board text variables (author, contact, license, project
  ID), which still had KiCad's placeholder values. This also clears the DRC
  error about an unresolved text variable in the drawing sheet.
- Changed the 3D model paths from `KICAD9_3DMODEL_DIR` to
  `KICAD10_3DMODEL_DIR`.
- J19: corrected the datasheet link, and the schematic value from "d" to
  10146070-112Y0LF.
- ERC: the root-sheet hierarchical labels `G_12V` and `{PLS_POWER}` are now
  local labels, and GND has a power flag. The net names are unchanged.
- Did not include the production packages, BOM, position and IPC netlist
  exports, and the vendor 3D model of the original repository.

## [V1] - 2026-03-10

First manufactured revision (March 2026).
