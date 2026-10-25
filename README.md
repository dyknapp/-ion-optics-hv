# Ion-optics HV supply - KiCad derivative

This is Daniel Knapp's configurable-polarity derivative of
[JQIamo/hv-piezo-driver](https://github.com/JQIamo/hv-piezo-driver), intended
for electrostatic ion-optics electrodes with a maximum output magnitude
of 250 V. Polarity is selected using zero-ohm links and a solder jumper
with power off and the output discharged.

- Open [the KiCad project](hardware/kicad/Eurocard-HV-piezo-driver.kicad_pro)
  using KiCad 10.0.7 or a compatible later version.
- Read the [project and configuration guide](hardware/kicad/README.md).
- Read the [dated changes and validation baseline](docs/MODIFICATIONS.md).

The original Eagle design, firmware, front-panel files and licence remain
in their original locations. The KiCad files in `hardware/kicad/` are the
editable source of the derivative. The inherited firmware has not yet been
adapted for this hardware's headless/I2C operation. This is an unvalidated
prototype baseline with documented outstanding rule-check findings.

## Provenance and documentation location

Initial derivative baseline: **2026-10-25**, on branch `ion-optics`, based on
upstream commit `8acd7d409c022f61565c32963c6fade5bf424614` (including the
PlatformIO update). The PCB is marked **PROTOTYPE - REV A**; that marking
does not identify a tested fabrication release.

Modified by Daniel Knapp with Codex assistance. Hardware documentation,
including the modified editable CAD, remains under [CERN OHL v1.2](LICENSE).
Retain upstream attribution and the separate firmware/library licence notices.

**Derivative public documentation URL: pending publication.** The board's
current link identifies the upstream design only. Once the derivative's
GitHub URL is established, update this notice and the board link/QR code
together in a separate commit. The original upstream notice is retained below.

**Signed: Codex (OpenAI), 2026-10-25.** This signature covers the derivative
notice above; the original project README below retains its upstream authorship.

---

# High voltage, low noise piezo driver

For more details, see our paper on the ArXiv: [[http://arxiv.org/abs/1609.03607]]

Release notes:

**v1.2**

* Teensy 32-bit ARM microcontroller used (MK20DX256VLH7)
* Voltage monitor now directly proportional to output voltage
* LP corner capacitor switch now properly biased at 2.5V (may have been true in v1.1 too).

**v1.2 errata**

* Voltage reading on MCU ADC not linear over full span; will require buffer after voltage divider.

Planned implementation in codebase:

* "unlimited digital integrator" -- uController integrates mod_in to zero with some given timeconstant. (DONE)
* scan module -- generate output scan/sweep. (easy to do, but need to find time. Stub `scan_module.h` file in code already)
* easier integration with eurocard backplane
* ... other?
