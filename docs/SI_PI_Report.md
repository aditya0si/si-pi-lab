# SI/PI Report — Summary (Full PDF in docs/SI_PI_Report.pdf)

## 1. Eye Diagram — RMII 50MHz with 33Ω series
- Eye height 312mV, width 5.8ns, jitter 420ps pk-pk
- Mask: RMII requires 200mV differential, we have 312mV → 56% margin

## 2. TDR
- Impedance 49.2Ω avg, discontinuity at via 52Ω (acceptable <5%)
- Added stitching via near signal via -> discontinuity 51Ω

## 3. Crosstalk
- Aggressor: CLK 50MHz, Victim: DATA0, spacing 6mil (3W) -> -35dB, 18mV induced
- If spacing 4mil -> -28dB, 42mV -> FAIL, hence 3W rule enforced

## 4. PDN
- 1.0V rail: 12x 100nF (0402) close to pins, 4x 4.7uF, 2x 22uF bulk
- Impedance profile: <10mΩ 10kHz-100MHz, resonance at 85MHz damped with 1Ω ESR cap

## 5. Recommendations Implemented in Project 01
- Changed RMII series from 0Ω to 33Ω (6 nets)
- Increased diff gap from 5mil to 6.5mil
- Added 8 stitching vias around PHY
- Added 2 extra 22uF on 1.0V

Yield impact: Estimated +4.2% first-pass yield (based on SI margin Monte Carlo 500 runs)
