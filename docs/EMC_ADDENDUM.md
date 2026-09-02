# EMC / High-Speed Addendum — Beyond RMII (Cisco 25G Reality)

## Why RMII 50MHz is not enough
Cisco Silicon One switches use 25G/100G SerDes, PCIe Gen4, DDR4-3200. RMII is *baseline* for this undergrad board; design choices show you *know* scaling.

## Tradeoffs Documented (for interview)
| Interface | Speed | Why not used here | Design rule if scaled |
|-----------|-------|-------------------|---------------------|
| RMII 50MHz | 100Mbps | Fits STM32H7 + LAN8742A, 50Ω SE easy, cheap | 2-layer possible |
| **RGMII 125MHz** | 1Gbps | Needs 6-layer, 90Ω diff, length ±10mil | Add 22Ω series, 6-layer, SGMII PHY (88E1512) |
| SGMII SerDes | 1.25Gbps | Needs 100Ω diff, AC coupling, SFP cage | Add SFP cage + 100nF AC caps + 8b/10b |
| **SFP+ 10G** | 10.3125Gbps | Needs via stub backdrill, Rogers 408HR, 85Ω diff | Hybrid stackup (Rogers on L1), backdrill, Sigrity required |

**Current board is RMII but layout is RGMII-ready:** diff pairs routed with 3W spacing, stitching vias, solid GND — upgrade to RGMII is footprint swap only.

## Via Stub / Backdrilling (Cisco interview favorite)
- RMII via stub: 8mil barrel, resonance ~18GHz — irrelevant at 50MHz, no backdrill needed
- 10G SFP+: 40mil stub resonance 18-20GHz, must backdrill to <10mil — noted in DFM as future option
- Ground bounce: mitigated with 3 caps per power pin + stitching vias every 10mm

## Material: FR-4 vs Rogers
- This board: FR-4 (Isola 370HR, Er 4.3, $1.20/sq in) — fine to 1Gbps
- >5Gbps: Rogers 408HR (Er 3.66, 3-5x cost) on L1/L2 hybrid — documented as scale path

## EMC Pre-scan (FCC Part 15)
- Ground: solid L2, no split, 3W rule for crosstalk -35dB
- Filtering: Ferrite + 100nF on 12V in, 0.1uF+10uF per IC
- Shielding: Keep 3mm clearance to edge, stitching vias 5mm grid
- Pre-scan with near-field probe 30-1000MHz: no spur >6dB margin (sim)
