# Additional 3D models

The PCB uses these models to show the tester in its system context: the iWave development kit on the FMC connector and the pulser frontend in the PCIe slot. They are not part of this repository. Save them here under the listed file names to get the complete 3D view.

| Board ref. | Model | Expected file name | Source |
|---|---|---|---|
| J19 (FMC) | iWave iW-RainboW-G35D Zynq UltraScale+ ZU19EG development kit | `iW-RainboW-G35D-3D.STEP` | iWave product page (vendor model, not redistributable) |
| J5 (PCIe x16) | MAGIE pulser frontend | `MAGIE_pulser_frontend.step` | Export from [`../../pulser_frontend`](../../pulser_frontend) (see below) |

To export the pulser frontend model, run this from `hw/pulser_frontend/`:

```bash
kicad-cli pcb export step --subst-models -o ../frontend_tester/3d_vendor/MAGIE_pulser_frontend.step MAGIE_pulser_frontend.kicad_pcb
```
