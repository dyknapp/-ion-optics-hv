# Ion-optics derivative: modifications and baseline

**Date: 2026-10-25. Modifier: Daniel Knapp, with Codex assistance.**

This initial derivative commit records the reviewed KiCad prototype for
electrostatic ion-optics electrodes. It is based on the JQI Eagle v1.2
hardware and retains upstream Git history through
`8acd7d409c022f61565c32963c6fade5bf424614` (PlatformIO compatibility update,
2025-09-09). Original Eagle, firmware, front-panel and licence files are
retained; the current derivative CAD is in `hardware/kicad/`.

This is one consolidated baseline of the completed edits. It does not
reconstruct intermediate design stages as historical Git commits. Earlier
working notes outside this repository may describe superseded switch/relay
arrangements or routing work that is now complete.

## CAD conversion and import repairs

- Converted the six-sheet schematic and four-layer layout to KiCad 10.0.7.
  Included project-local symbol/footprint libraries and portable library tables.
- Repaired schematic-to-PCB associations and import metadata, including
  courtyard classification and logical connectivity differences identified
  during the import review.
- Restored original bottom/inner plated-through-hole annuli while preserving
  the top copper and drill dimensions.
- Restored the original 0.1524 mm general copper clearance and relaxed the
  USB clearance to 0.20 mm.
- Kept the nominal 100 x 160 mm Eurocard layout and custom DIN 41612 backplane
  connector arrangement. The front cutout and Edge.Cuts embedded in the J1
  footprint remain part of the mechanical design.
- Normalized copied CAD/library text to LF line endings and cleared indentation
  from otherwise blank lines for Git. These are formatting changes only;
  they do not change the reviewed circuit or geometry.

## Output polarity, setpoint and monitor

The derivative targets an output magnitude up to 250 V with manual polarity
selection. The flyback and main regulator gain magnitudes are retained.

- Added R86-R96 and JP6 around the revised setpoint/monitor and polarity network.
  Reused U6B as a unity-gain setpoint inverter.
- The final HV crossover is after R14/R15, at the HV_PRE_FILTER_IN and
  HV_PRE_RETURN_IN nodes, feeding the following filter through R6/R18.
  It is not the earlier proposed direct crossover at the rectifier output.
- R91-R94 use 2010 zero-ohm link footprints; R95/R96 use 0603 footprints.
  JP6 is an open solder-jumper footprint.
- Relay interfaces J6/J7 and draft switching arrangements were removed during
  layout work. Relay coils, drivers, state sensing and live polarity changes
  are not implemented.

| Output polarity | Fit | Omit | JP6 |
| --- | --- | --- | --- |
| Positive | R91, R92, R95 | R93, R94, R96 | Open |
| Negative | R93, R94, R96 | R91, R92, R95 | Bridged |

Power off and discharge before changing links. Default fitted/DNP flags
describe the positive configuration; negative assembly needs the opposite
population and JP6 bridged.

For R42 = 4.99 Mohm, R43 = 100 kohm, R88 = 300 kohm and
R89 = R90 = 100 kohm, the nominal transfer at **U6A pin 1** is:

- Positive: `Vmon = VHV / 50.9`.
- Negative: `Vmon = -VHV / 50.233333...`.

Negative-mode magnitude sensitivity is approximately 1.327% higher. These
equations neglect component tolerances, bias currents and downstream loading.
MON0 is downstream of R34 = 49.9 ohm and is loaded by the ADC-divider path
and any external instrument; the equations are not an exact connector
calibration. Both intended configurations give a positive monitor magnitude,
so the monitor alone does not independently verify output sign.

## Removed circuitry and retained controls

- Removed the quench branch: C34, R2, R22, R24, R26, R28, R29, R30, R32 and T2.
  Its active output-discharge action is therefore absent.
- Removed the original switchable-filter bias/control parts C72, R40, R46,
  R48 and SW1. The slow setpoint filter remains connected, with C71 returned
  to ground. Original SW1 should not be confused with a later draft monitor
  switch that was ultimately replaced by JP6.
- Removed LCD-only LCD0, U5, R1, C48, IC6, C50 and D6, and their dedicated
  schematic wiring/PCB copper. Freed U9 pins 41-44 have no-connect markers.
- Retained the encoder, shared DAC SPI routes, D5 rail clamp, U9
  MK20DX256VLH7 MCU and required PJRC-programmed MKL02 bootloader device.
  Neither an AVR migration nor a higher-resolution ADC was implemented.
- Retained I2C hardware connections to J1 A14 (SDA) and C14 (SCL).

## Procurement and layout changes

- Applied 163 reviewed part assignments with MPN, Manufacturer, LCSC and
  JLCPCB data. `PARTNO` records the chosen MPN and `OriginalOrderCode`
  preserves the previous distributor code. External-only selections have
  blank JLCPCB fields. These are dated sourcing results, not guaranteed stock.
- Examples include D1 DHVSD3004SSQ-7, C19 C1608X8R1H473K080AA,
  U3/U4 LM7171AIMX/NOPB, SW2 KXT321LHS, X2 CS00530-16M, and 700 V TNPV
  resistors at R19/R10/RI_0. The CAD fields are the complete assignment record.
- D3/D5/D7 use PMEG3005EJ,115 in SOD-323F footprints. Their symbols map
  cathode to pin 1 and anode to pin 2. Subsequent rerouting includes the
  deliberate D5 cathode connection to +5 V.
- C23 was not automatically substituted. Original ordering code
  `399-9040-1-ND` remains; `CL10C222JB8NNNC` / `C33353` is candidate-only
  pending qualification of the original dielectric/requirements.
- Added 1.25 mm copper-clearance rules on F.Cu/B.Cu for the union of both
  polarity configurations: pre-positive, pre-negative, selected-output and
  flyback switching-node families. Internal layers and spacing within each
  defined RC branch retain their existing rules. Existing violations remain.
- Updated silkscreen for polarity, J3 shunts, backplane/I2C contacts,
  J2/J4 patch rows and MCU debug pins. Relocated R95/JP6 labels. Marked the
  derivative "ION OPTICS HV / PROTOTYPE - REV A", retained JQI attribution,
  and replaced the obsolete QR graphic with an explicit upstream URL.
  A derivative-specific URL/QR is deferred until its GitHub location exists.

## Validation baseline and limits

The reviewed design has 170 schematic components, 171 PCB footprints
(including the retained board-only JQI artwork), 185 schematic nets and
607 PCB pads. The preceding connectivity review found matching multi-pin
schematic/PCB net memberships and no unconnected PCB items.

At the 2026-10-25 silkscreen baseline, native KiCad DRC with zone refill and
schematic parity reported:

| Check | Result |
| --- | --- |
| PCB unconnected items | 0 |
| DRC findings | 557: 84 errors and 473 warnings |
| Copper-clearance errors | 32 |
| Prohibited-area errors | 49 |
| Courtyard-overlap errors | 3 |
| Schematic-parity findings | 137, unchanged |
| Schematic ERC findings | 142, unchanged |

These counts were reproduced after relocating the project into this repository.
The 137 CLI parity findings comprise 111 extra-footprint and 26 net-conflict
reports associated with the imported hierarchy/net naming. The connectivity
conclusion comes from a separate complete six-sheet netlist-to-PCB membership
comparison, in addition to the zero-unconnected result.

The last silkscreen edit reduced DRC findings from 581 to 557 entirely through
text/silkscreen improvements. An independent comparison preserved all 1,386
segments, 549 vias, 32 arcs, 63 zones, pads/padstacks, electrical properties,
component positions and Edge.Cuts. That edit removed only the zero-pad QR
footprint from the component inventory. The edited annotations had no DRC
findings. Other component-reference/silkscreen findings remain.

These results do not make the board DRC-clean. The remaining clearance
exceptions were retained for the prototype; published results for the original
positive-only board do not establish the derivative's performance. No bench
validation of this derivative is documented. Negative-mode behavior, noise,
settling and discharge after quench removal remain to be measured.

## Firmware and later work

The inherited PlatformIO-capable firmware is unchanged. It remains
LCD/menu-driven and includes LP_EN control for circuitry removed from this
revision. It needs adaptation for headless operation, the selected polarity,
monitor calibration and an I2C peripheral/slave command interface. No
Ethernet-to-I2C bridge implementation or multi-board address scheme was added.
The retained MCU ADC limitations have not been resolved by adding a new ADC.

The original front-panel files also remain unchanged and need new interface
cutouts. Before a fabrication release, review the remaining rule findings,
qualify unresolved parts and record the exact CAD, BOM and firmware revisions.

## Licence, publication and authorship

The original [CERN OHL v1.2 licence](../LICENSE) and upstream attribution are
retained. Modified hardware documentation is provided under the same licence.
The hardware licence does not automatically extend to firmware; preserve the
existing software and bundled-library notices separately.

Derivative public Documentation Location: **pending publication**. The editable
sources are in this repository's `hardware/kicad/` directory. Once the GitHub
origin is established, add the actual public documentation URL to the root
README and this notice, and update the board link/QR in the same follow-up
commit. The current upstream link is an attribution link, not the location of
the derivative's sources.

**Signed: Codex (OpenAI), 2026-10-25.** Authorship signature for this document.
