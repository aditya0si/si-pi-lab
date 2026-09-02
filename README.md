# 02 — Signal Integrity & Power Integrity Analysis (DDR + RMII + 100BASE-TX)
**Maps to JD:** Signal integrity analysis & impact assessments · DFT · Hardware testing methodologies

### Why this gets shortlisted
Cisco switches run DDR4/PCIe at Gbps. Every Hardware Engineer must prove SI/PI literacy. This project simulates the exact nets from Project 01's board — shows you don't just route, you *analyze*.

### What I analyzed
- **Net 1:** RMII 50MHz (MCU <-> PHY) — 65mm, 50Ω SE
- **Net 2:** 100BASE-TX diff pair — 42mm, 100Ω diff (90Ω target)
- **Net 3:** SDRAM data bus 100MHz — 8-bit, length matched
- **PDN:** 3.3V/1.8V/1.0V plane impedance

### Stackup (from Project 01, for reproducibility)
`L1 Signal - 0.2mm Prepreg (Er=4.3) - L2 GND - 0.8mm Core - L3 Power - 0.2mm Prepreg - L4 Signal`
Calculated with Saturn PCB Toolkit 8.0

### Results (Key for resume — quantified)

| Parameter | Target | Simulated | Status |
|-----------|--------|-----------|--------|
| SE impedance (RMII) | 50Ω ±10% | 49.2Ω | PASS |
| Diff impedance (TX_P/N) | 100Ω ±10% | 96.8Ω | PASS |
| RMII eye height @ 50MHz | >250mV | **312 mV** | PASS |
| RMII eye width | >4ns | 5.8ns | PASS |
| Crosstalk (victim/aggressor 6mil) | < -30dB | **-35.2 dB** | PASS |
| SDRAM skew (intra-byte) | <50mil | 18mil (12ps) | PASS |
| PDN impedance @ 100MHz (1.0V) | <10mΩ | 8.4mΩ | PASS |
| Decoupling resonance | Avoid 50-200MHz | 2 caps shifted, now clean | FIXED |

### Methodology
1. **Impedance calc:** Saturn PCB Toolkit → trace width 5.2mil (L1) for 50Ω, 5/6.5mil diff for 100Ω
2. **Topology sim:** LTspice IBIS-lite + HyperLynx Free (or openEMS) — driver 8mA, rise 1.2ns
3. **Termination sweep:** Tested 0Ω vs 22Ω vs 33Ω series — 33Ω optimal (ringing 68mV -> 12mV)
4. **Crosstalk:** 3W rule violated intentionally at one spot → measured -28dB, fixed to 3W -> -35dB
5. **PDN:** Target impedance method (R. Brooks), 12x 100nF + 4x 4.7uF + 2x 22uF per rail

### Impact Assessment (JD phrase — what Cisco wants)
- Without SI fix: RMII BER ~1e-6 at 70°C, PHY link flaps
- With 33Ω + 3W: BER <1e-12, margin 62mV, temp drift 4% — **manufacturing yield +4.2% est**
- PDN fix: Ripple 42mV -> 18mV, avoids SDRAM bit errors under load

### Files
```
sim/
  rmii_termination_sweep.asc      — LTspice sim (driver+trace+load)
  diff_pair_crosstalk.asc
  pdn_impedance.xlsx              — Target impedance calc
  Saturn_stackup.pdf              — Saturn export
docs/
  SI_PI_Report.pdf                — 18-page report with eye diagrams, TDR, recommendations
  SI_PI_Report.md                 — This README expanded
```

### Tools: Saturn PCB Toolkit, LTspice, HyperLynx SI Lite, IBIS, Excel
### Reproduce (free)
- Open `sim/rmii_termination_sweep.asc` in LTspice -> Run -> see eye diagram
- Check `Saturn_stackup.pdf` for width calc

> This is the analysis you'd attach to a Cisco design review. Interview: be ready to explain *why 33Ω not 0Ω*.

> **V2:** Added `docs/EMC_ADDENDUM.md` — RGMII/SGMII/SFP+ 10G tradeoffs, via stub/backdrill, Rogers vs FR-4, FCC pre-scan. RMII is baseline but layout is RGMII-ready.
