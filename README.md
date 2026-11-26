# LDO Regulator with a Custom Bandgap Reference (180 nm CMOS)

An on-chip **low-dropout (LDO) regulator** built around a **custom-designed cascoded beta-multiplier bandgap reference (BGR)**.
Both blocks are designed at transistor level in **Cadence Virtuoso** and verified in **Spectre** across PVT corners and Monte Carlo runs.

Circuit schematics, device sizing, hand calculations and the detailed simulation results are in the reports in [`reports/`](reports/).

## At a glance

| | Bandgap Reference | LDO Regulator |
|---|---|---|
| Technology | 180 nm CMOS, 3.3 V devices | 180 nm CMOS, 3.3 V devices |
| Supply | 3.3 V (2.97–3.63 V) | 3.3 V (2.97–3.63 V) |
| Output | **1.255 V** | **2.4 V**, 0–2 mA load |
| Power / current | **5.05 µW** | **39.9 µA** quiescent (≤ 55 µA over PVT) |
| Key result | **25.9 ppm/°C** worst case over PVT | **77.9–92.3° phase margin**, **3.57–8.13 MHz UGB** at full load |
| Statistical | ±4.84 % worst case (PVT + 200-sample MC) | **±0.37 % (3σ)** output after 5-bit trim (200-sample MC) |

## 1. Bandgap Reference

**Architecture:** a self-biased **cascoded beta-multiplier** core with PNP bipolar devices. Its PTAT and CTAT components are summed through resistors to produce a temperature-compensated reference. It includes:
- a **native-NMOS start-up circuit** that turns itself off once the core is biased
- an **enable** input for power-down

Typical values are at tt, 27 °C, 3.3 V. Spreads cover 81 PVT corners and 200 Monte Carlo samples.

| Parameter | Typical | PVT / MC |
|---|---|---|
| VREF | 1.255 V | 1.249–1.263 V over PVT; MC σ 17.6 mV |
| Accuracy (PVT + MC) | — | −4.68 % / +4.84 % |
| Temperature coefficient (−40 to 125 °C) | < 0.1 ppm/°C | −6.0 to 25.9 ppm/°C |
| Power | 5.05 µW | ≤ 8.98 µW |
| PSRR @ 1 Hz | 69.9 dB | ≥ 47.4 dB |
| Line sensitivity | 0.68 mV/V | 0.38–2.51 mV/V |
| Start-up settling | 16.1 µs | 10.8–44.2 µs |
| Power-down current | 42 pA | — |

## 2. LDO Regulator

**Architecture:**
- **PMOS pass transistor** (125 µm / 0.3 µm)
- **5-transistor OTA** error amplifier with a resistor-referenced bias (18.5 µA tail)
- **Miller compensation with a nulling resistor** (Cc = 1.5 pF, Rz = 12 kΩ), plus a 1 pF reference capacitor
- **240 kΩ feedback divider with a 5-bit binary-weighted fuse-link trim**
- **100 pF on-chip output capacitor**
- **Enable** with a pass-gate pull-up for shutdown
- **Reference:** the bandgap from Section 1

Typical values are at tt, 27 °C, 3.3 V. Spreads cover 81 PVT corners.

| Parameter | Typical | PVT / MC |
|---|---|---|
| Output voltage | 2.400 V (no load) / 2.398 V (2 mA) | — |
| Loop gain, full load | 80.4 dB | — |
| Phase margin, full load | 85.6° | 77.9–92.3° |
| Unity-gain bandwidth, full load | 5.29 MHz | 3.57–8.13 MHz |
| Gain margin, full load | 20.7 dB | 15.5–27.0 dB |
| Quiescent current (no load) | 39.9 µA | 29.3–55.0 µA |
| Load regulation (0–2 mA) | 1.37 mV/mA | 0.79–3.80 mV/mA |
| PSRR at DC (no load / full load) | 51.3 / 62.3 dB | — |
| Output spread, Monte Carlo (200) | — | ±4.4 % (3σ) before trim → **±0.37 % (3σ)** after trim |
| Load impulse (+10 mA, 1 ns, on 2 mA) | 99.5 mV undershoot | — |
| Load pulse 0 → 2 mA → 0 | 73.7 mV undershoot / 52.6 mV overshoot | — |
| Start-up after enable | 11.8 µs | — |
| Shutdown current (EN = 0) | 3.1 nA | — |

## Verification

Both designs were characterised in Cadence ADE Maestro.
- **PVT:** 81 corners: MOS process (ss/tt/ff) × resistor process (ss/tt/ff) × supply (2.97 / 3.3 / 3.63 V) × temperature (−40 / 27 / 125 °C).
- **Monte Carlo:** 200 samples, process and mismatch.
- **LDO analyses:**
  - stability (loop gain, phase and gain margin) at no load and full load
  - PSRR
  - DC load regulation
  - temperature sweeps
  - trim-code selection, and trim verification on worst-case Monte Carlo samples
  - power sequencing
  - load-pulse and 1 ns load-impulse transients
- **BGR analyses:**
  - temperature coefficient
  - line sensitivity and minimum supply
  - PSRR
  - start-up behaviour
  - power-down

The reports compare hand calculations of the poles, zeros and bias points against simulation.

## Repository contents

```
reports/
├── BGR_Report.pdf   Bandgap reference: topology, sizing, hand analysis, PVT/MC results
└── LDO_Report.pdf   LDO regulator: architecture, compensation, trimming, PVT/MC/transient results
```

## Tools

Cadence Virtuoso (schematic entry), Spectre (DC, AC, STB, transient, PVT and Monte Carlo through ADE Maestro).

## Author

**Jai Srikar Medarametla**, B.Tech ECE, IIIT Hyderabad
