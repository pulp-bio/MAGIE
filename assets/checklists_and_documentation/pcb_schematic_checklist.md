# Schematic review checklist
Copied and modified with permission from Mark Harris, original link: <https://resources.altium.com/p/pcb-design-and-review-checklist>
## Style

- [x] Does each schematic sheet have the correct template applied?
- [x] Is the title block filled out on each sheet?
- [x] Can the sheet sizes be printed on your office’s printer?
    -  Sheets should not exceed Tabloid/A3 size.
- [x] Are all schematic sheets the same size?
- [x] Do nets have netlabels to ease readability and PCB layout?
- [x] Are nets consistently labeled?
- [x] Are power ports consistently labeled?
- [x] If there are multiple sheets:
    - [x] Is there a top sheet?
    - [x] Does the top sheet show connections between each sub-sheet?
    - [x] Do you need a table of contents? no
- [x] Does the schematic design compile and pass design rules without errors? aware of the ones still present
- [x] Has each warning from compiling the schematic design been checked to see if it is actually critical? check again before gerber file generation
- [x] Is there a change list or revision list if this is not the first design review for the schematic? no, first version
- [x] Are all components aligned to the grid, and are all the net wires correctly connected to each pin?
- [x] Can groups of offsheet connections be grouped into a bus, or harness to improve the legibility of the top sheet?
- [x] Are multi-channel blocks implemented as multi-channel sheets?
- [x] Are differential pairs correctly identified with _P and _N signals?
- [x] Do differential pairs have a differential pair label?
- [x] Are there net classes applied to each net that needs it?
    - [ ] High voltage
    - [ ] High current
    - [x] RF/Impedance matched
    - [x] Differential pairs
- [x] Do all components have the correct designators? (i.e.: You annotated the schematic)
## Usability/Testing
- [x] Is there a LED for each power rail? At least for the input? only PS & PL
- [x] Are there test points for each power rail?
- [x] Are there test points for critical signals/signals of interest? only I2C, PGs & PWROFF
- [x] Are there simple ways to connect ground, for instance for an oscilloscope?
## Production
- [x] Can any component values be safely merged to reduce BOM line items?
- [x] Is only one part number specified for each passive component value with a specific size?
- [x] Check that each symbol has a manufacturer and part number assigned.
- [x] Are all components active production, and not end-of-life/discontinued/not recommended for new designs at the time of the schematic review?
- [x] Does each component have sufficient stock in the supply chain?
## Connectors
- [x] Check that I/O pins have a pull-up or down to define their default state when disconnected.
- [x] Ensure there is a decoupling capacitor for each power pin on the connector.
- [x] Do electrically noisy pins need to be treated as pseudo-differential pairs?
- [x] Do long cables need to be isolated for EMC? no
## Components
- [x] Check all symbol pinouts against the datasheet, even if you have used the IC before or trust the symbol source. maybe check again (placement long ago)
- [x] Tantalum Capacitors' voltage rating is at least 20% higher than the maximum expected voltage.
- [x] Have you checked the current passing through each resistor for power dissipation?
    - [x] Is it safely within the rating of the components?
    - [x] Will the temperature of the resistor be too high, even if it’s within the ratings?
- [x] Is each resistor’s voltage rating sufficient for the maximum voltage applied (mostly relevant to 0402 and smaller sizes)?
- [x] Have all the current limiting resistors been correctly calculated for each LED's forward voltage?
- [x] Have all the current limiting resistors been correctly calculated for each optoisolator forward voltage?
- [x] Do reset/enable pins need an external pull-up or pull-down resistors?
- [x] Are any potentially floating pins pulled up or down with external resistors?
    - [x] Pins drove with diodes
    - [x] Pins drove with transistors or MOSFETs
    - [x] Comparator outputs don't think we have any
- [x] Are any pins with a state that is critical at power up externally pulled up or down?
    - [x] Gate/base pins on MOSFETs and transistors
    - [x] External connectors
    - [x] Safety devices
    - [x] Thermal management devices
- [x] Do any programmable devices have a programming header/pad accessible?
    - [x] For prototypes, can you read/write flash/EEPROMs externally with a connector?
- [x] Is each operational amplifier and comparator connected with the correct polarity?
- [x] Are any unused devices in array components (e.g.: quad operational amplifiers) correctly terminated or tied to a rail?
- [x] Check for the correct polarity of each diode or LED.
## Interfaces 
- [x] Is the UART interface connected correctly? (TX connected to RX and RX connected to TX)
- [x] Are there Pull up resistor connected to the I2C? What value is needed for the desired frequency? 
- [x] Are there test points for the interfaces 
## Power
- [x] Every power pin of every IC should have a decoupling capacitor.
- [x] Has each switched mode voltage regulator been simulated to ensure it is stable across all load conditions?
- [x] Has each switched mode voltage regulator been simulated to ensure it can supply the required current?
- [x] Has each switched mode voltage regulator been simulated to ensure it can provide the required voltage under all load conditions?
- [x] Do multiple voltage rails need sequencing?
    - [x] Is it implemented?
- [x] Do you need a reset supervisor IC on any devices if a regulator has a soft start?
- [x] Has the power distribution network been analyzed for a total load? Can the regulators supply this load?
- [x] Should any subsystems have isolated supplies?
- [x] Should any subsystems have separately regulated supplies?
- [x] Does each voltage regulator’s output meet the precision requirements for the subsystems connected to it?
- [x] Is the total capacitance connected to each regulator within its ability to supply?
- [x] Is there sufficient input capacitance on each regulator to prevent reverse supply under changing loads?
- [x] Are any input voltages to regulators able to drop below the regulator’s minimum operating voltage?
- [x] Are any input voltages to regulators able to exceed the regulator's maximum voltage?
- [x] Is there a linear regulator between any devices that need extremely clean power (e.g.: operational amplifiers with high gain) and a switched mode regulator source?
## Signal
- [x] Are there filters on every Analog to Digital converter pin?
- [x] Have amplifier circuits been simulated to ensure they are stable?
- [x] Have all voltage dividers been re-calculated to ensure the output voltage is correct?
- [x] Is there capacitance and/or a Zener diode on the output of each operational amplifier?
- [x] Are all signals feeding into logic devices within the maximum voltage rating?
## EMI/EMC/Protection
- [x] Does every externally accessible connection/exposed piece of metal have adequate ESD protection? Keep in mind that ESD spark gaps for what is exposed.
    - [ ] Connector pins
    - [ ] Connector housings
    - [ ] Metal shields on buttons
    - [ ] Pins of buttons
    - [ ] Displays
    - [ ] Potentiometers/dials
    - [ ] Card socket pins
    - [ ] Metal card housings
- [x] Does the input power require a fuse to protect upstream devices?
- [x] Is the input power protected against reverse polarity?
- [x] Is there overvoltage protection on the power input?
- [x] Is there overcurrent protection on the power input?
- [x] Are there current limiting resistors on nets from external devices to sensitive devices (e.g.: microcontroller pins)
- [x] Do all optoisolators have parallel resistors/capacitors with their diode for noise immunity?
- [x] Is there input under-voltage protection?
- [x] Is there short circuit protection on each voltage rail that has an external connection/output?
- [x] Does every switching regulator have a sufficient input filter to prevent conducted EMI from escaping on its input?
- [x] Are MOSFETs protected against voltage transients with external diodes?!
