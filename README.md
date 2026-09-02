# si-pi-lab — Signal & Power Integrity Lab

[![CI](https://github.com/aditya0si/si-pi-lab/actions/workflows/ci.yml/badge.svg)](https://github.com/aditya0si/si-pi-lab/actions)

<p align="center">

[![Live Demo](https://img.shields.io/badge/Live%20Demo-GitHub%20Pages-0ea5e9?style=flat-square)](https://aditya0si.github.io/si-pi-lab/)
  <img src="https://img.shields.io/badge/SI-eye%20312mV-success?style=flat-square" />
  <img src="https://img.shields.io/badge/PI-%3C10m%CE%A9-purple?style=flat-square" />
  <img src="https://img.shields.io/badge/crosstalk--35.2dB-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/stackup-4L%20Isola%20370HR-lightgrey?style=flat-square" />
  <img src="https://img.shields.io/badge/tools-Saturn%20%7C%20LTspice%20%7C%20HyperLynx-informational?style=flat-square" />
</p>

<p align="center"><i>Not just routing — measuring. Eye, TDR, crosstalk, and PDN for the same 4-layer board.</i></p>

### What was probed
- **RMII 50MHz** (MCU↔PHY, 65mm, 50Ω SE) — eye before/after 33Ω series
- **100BASE-TX diff** (42mm, 100Ω diff) — TDR + crosstalk
- **SDRAM 100MHz** — intra-byte skew < 50mil
- **PDN** — 3.3V/1.8V/1.0V plane impedance <10mΩ

### Results
| Parameter | Target | Measured | 
|-----------|--------|----------|
| SE impedance | 50Ω ±10% | **49.2Ω** |
| Diff impedance | 100Ω ±10% | **96.8Ω** |
| Eye height | >250mV | **312mV** (5.8ns width, 420ps jitter) |
| Crosstalk @6mil (3W) | < -30dB | **-35.2dB** |
| Skew | <50mil | **18mil** (12ps) |
| PDN @100MHz (1V) | <10mΩ | **8.4mΩ** |

Impact: ringing 68mV → 12mV with 33Ω, yield **+4.2%** (500-run Monte Carlo), ripple 42mV → 18mV.

### Interactive Lab
→ **`viewer.html`** — interactive eye diagram (drag termination 0Ω/22Ω/33Ω), impedance calculator (width → Ω), and crosstalk vs spacing slider. Pure HTML/JS, runs offline.

→ **`sim/rmii_termination_sweep.asc`** — LTspice, run and see eye.

### Beyond RMII (so it doesn't look like 2005)
RMII is the baseline; layout is RGMII/SGMII/SFP+ 10G-aware: via stub resonance ~18GHz (no backdrill needed at 50MHz, but backdrill <10mil at 10G), FR-4 vs Rogers 408HR hybrid noted. See `docs/EMC_ADDENDUM.md`.

### Files
`sim/` — rmii_termination_sweep.asc, diff_pair_crosstalk.asc, Saturn stackup  
`docs/` — SI_PI_Report.md, EMC_ADDENDUM.md

---
*Pairs with pcb-dfm-dft (same stackup) and pdn-thermal-lab.*
