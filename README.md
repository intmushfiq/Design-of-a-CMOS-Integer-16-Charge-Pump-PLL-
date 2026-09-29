# 1.2 GHz CMOS Integer-N Charge-Pump PLL — GPDK045

Transistor-level design of a type-II, 3rd-order integer-N charge-pump PLL in the Cadence **GPDK045 (45 nm)** process, built for the Analog Integrated Circuits Laboratory at BUET (Group 6).

**Tools:** Cadence Virtuoso IC6.1.5 · Spectre · ADE L · VDD = 1.1 V

## Architecture

```
f_ref (75 MHz) ──► PFD ──► Charge Pump ──► Loop Filter ──► VCO ──┬──► f_out (1.2 GHz)
                    ▲                                           │
                    └──────────────── ÷16 Divider ◄──────────────┘
```

| Block | Implementation |
|---|---|
| PFD | Dead-zone-free tri-state PFD with reset delay cell and output buffers |
| Charge Pump | Replica-bias CP (error amp on PMOS gate bias) with on-chip beta-multiplier current reference, I_CP = 50 µA |
| Loop Filter | 2nd-order passive (R + C1 ‖ C2) using PDK `resnsppoly` and `mimcap` devices |
| VCO | 7-stage current-starved ring oscillator |
| Divider | ÷16 (N = 16) |

## Specifications & Results

| Parameter | Target | Simulated |
|---|---|---|
| Output frequency | 1.2 GHz | 1.2000 GHz ✅ |
| Reference / N | 75 MHz / 16 | — |
| VCO tuning range | 0.9 – 1.5 GHz | 0.905 – 1.50 GHz ✅ |
| Loop bandwidth | 3.75 MHz | 3.757 MHz ✅ |
| Phase margin | 60° | 60.0° ✅ |
| Lock time | ≤ 8 µs | 1.51 µs ✅ |
| RMS period jitter | ≤ 8 ps | 1.23 ps ✅ |
| VCO phase noise @ 1 MHz | ≤ −90 dBc/Hz | −92.8 dBc/Hz ✅ |
| Reference spur | ≤ −40 dBc | ≤ −77 dBc ✅ |
| Power | ≤ 5 mW | 1.98 mW ✅ |

> Measured K_VCO (~1.06 GHz/V) was higher than the 750 MHz/V target, so the loop filter was rescaled to hold the bandwidth and phase margin.

## Verification Methods

- **Transient** — closed-loop lock, lock time, spur (FFT of control voltage/output)
- **DC sweep** — charge-pump UP/DN current matching over the control-voltage range
- **PSS + Pnoise** — VCO phase noise
- **Linear phase-domain model** (analogLib `vccs`/`vcvs`/RC) — loop bandwidth and phase margin
- **SKILL `cross()` script** in CIW — RMS period jitter extraction

## Repository Structure

```
├── schematics/      # Block and top-level schematics (PFD, CP, LF, VCO, DIV, PLL_TOP)
├── testbenches/     # Per-block and full-PLL testbenches
├── scripts/         # SKILL/OCEAN scripts (jitter extraction, etc.)
├── results/         # Waveforms and plots
└── docs/            # Report and presentation
```

## Status

- [x] Schematic design and full-PLL verification
- [x] Transistor-level charge pump with on-chip reference
- [ ] Full-custom layout
- [ ] DRC / LVS sign-off

## Team

Group 6 — Analog Integrated Circuits Laboratory, Department of EEE, BUET
