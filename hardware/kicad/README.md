# KiCad ion-optics prototype

Open `Eurocard-HV-piezo-driver.kicad_pro` with KiCad 10.0.7 or a compatible
later version. The project contains six schematic sheets and a four-layer
PCB. Its original filename is retained for schematic hierarchy and provenance.
This directory is the working source for the derivative; the Eagle files at
the repository root document the original design.

The project-local symbol library, `.pretty` footprint library, `sym-lib-table`
and `fp-lib-table` must travel with the CAD files. Both library tables use
`${KIPRJMOD}` paths. Install KiCad's standard symbol/footprint libraries as well;
several replacement footprints use them. No Eagle installation is required.

## Polarity configuration

The intended maximum output magnitude is 250 V. Change polarity only with
power off and the HV nodes discharged. Fit exactly one complete configuration:

| Output polarity | Fit zero-ohm links | Omit links | JP6 |
| --- | --- | --- | --- |
| Positive, default schematic population | R91, R92, R95 | R93, R94, R96 | Open |
| Negative | R93, R94, R96 | R91, R92, R95 | Bridged |

R91-R94 form the HV crossover. R95/R96 select the matching original/inverted
setpoint, and JP6 selects the monitor feedback configuration. Never fit both
sets: that can short the secondary path or connect the two setpoint outputs
together. For negative assembly, also change the fitted/DNP flags in the
schematic and any derived assembly BOM. The board has no relay-control or
automatic polarity-selection hardware.

The monitor reports positive magnitude for either correctly configured output
polarity. Its nominal gain differs between configurations, and it cannot
independently confirm the output sign. See [MODIFICATIONS.md](../../docs/MODIFICATIONS.md)
for the transfer equations and calibration caveats.

## Connections and selection headers

J1 is a DIN 41612 Type C connector with A/C rows populated. Its electrical
pinout is specific to this design; the Eurocard mechanics do not make it a
VME or other standard-bus module.

| J1 contacts | Signal |
| --- | --- |
| A1, C1 | GND |
| A30, C30 | -15 V |
| A31, C31 | +15 V |
| A25, C25 | HV_INTERLOCK |
| A14 | I2C_SDA |
| C14 | I2C_SCL |

The I2C connections are present in hardware. I2C peripheral/slave commands,
address selection, multi-board operation and headless control are not yet
implemented in the retained upstream firmware.

For J3 modulation-source selection, install either:

- Front BNC: shunts **3-4 and 5-6**.
- Backplane: shunts **1-3 and 5-7**.

The board diagrams show the top view. J3 pins 2 and 8 are unused. The front
modulation BNC has a differential signal return; do not assume its shield is
board ground.

J2/J4 patch rows 0-7 correspond to backplane channels, not MCU GPIO. In top
view, the four columns are GND, MOD, GPIO and OUT. MOD alternates positive
on rows 0/2/4/6 and negative on rows 1/3/5/7. OUT carries MON on rows 0-3
and AUX_DAC on rows 4-7. A same-row shunt from a J4 even pin to the adjacent
J2 odd pin selects a backplane modulation channel; a within-J2 shunt routes
MON/AUX to that backplane channel. A within-J4 shunt grounds the corresponding
modulation input.

J5 is the 2 mm MCU debug header. Pins 1-7 are 3V3, GND, TCK/SWCLK,
TDO/SWO, TDI, TMS/SWDIO and nRESET. The retained MK20 MCU needs the specified
PJRC-programmed bootloader device; a blank MKL02 is not an equivalent part.

## Editing, checks and fabrication records

Track the `.kicad_pro`, `.kicad_pcb`, `.kicad_dru`, all six `.kicad_sch`
files, local libraries and library tables. The repository ignores local UI
state, locks, caches, `.history/` and automatic backups. Curated BOM CSV/XML
files remain trackable. Optional `generated/`, `plots/` and `gerbers/`
directories here are ignored scratch locations; put deliberate release
packages in a separate versioned release directory.

Run ERC and PCB DRC, including zone refill and schematic parity, after changes.
The current baseline is not rule-clean; compare with the dated results in
[MODIFICATIONS.md](../../docs/MODIFICATIONS.md). The 1.25 mm HV rules are for
outer copper and specified net families, not a blanket clearance on every
layer or an independent creepage assessment. Review their exact net-name
lists after changing labels or reannotating the circuit.

Selected MPN, manufacturer and JLCPCB/LCSC fields are stored in the schematic.
C23's proposed replacement is explicitly a candidate rather than an approved
substitution. The sourcing work is a 2026-10-25 snapshot, not a stock guarantee.

Before a fabrication release, settle the remaining design/firmware decisions,
generate the matching BOM and manufacturing files, and tag that exact commit.
The inherited front-panel design still includes the original interface and
needs updating for the LCD-free instrument. The board's "PROTOTYPE - REV A"
marking is not evidence of bench validation.

Hardware documentation is licensed under [CERN OHL v1.2](../../LICENSE).
Derivative documentation URL: pending publication; update the root README
notice and board QR/link when the GitHub origin is established.

**Signed: Codex (OpenAI), 2026-10-25.** Prepared for Daniel Knapp from the
reviewed schematic, PCB and modification records.
