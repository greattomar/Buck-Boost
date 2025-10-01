# 4-Phase Interleaved Cuk-SEPIC Converter — Simulink Model

## Overview
This repository contains a Simulink model of a **4-phase interleaved Cuk-SEPIC** DC–DC converter designed to operate from a **12 V input** and provide **dual outputs: +15 V (6.67 A) and -15 V (3.87 A)**.  
The model includes switching MOSFETs, input inductors, coupling/transfer capacitors, output filters, diodes, control (PWM generation / gating), and measurement blocks. The project also contains waveform screenshots produced from the simulation.

The model was built at a switching frequency of **100 kHz** (see “Notes” below).  

---

## Repository contents
- `model/` — (Simulink files) the full Simulink model files (.slx/.mdl).  
- `Images/` — waveform images exported from the simulation:
  - `I1_waveform.png`
  - `I2_waveform.png`
  - `I3_waveform.png`
  - `I4_waveform.png`
  - `I_battery_waveform.png`
  - `I_load_waveform.png`
  - `Vo_plus_waveform.png`
  - `Vo_minus_waveform.png`
- `README.md` — this file.
- `docs/` — optional supporting documentation (if present).

---

## Simulink Model & Model parameters

![Figure 1: High-level block diagram of the converter in Simulink.](Images/sss_200.png)

| Component / Parameter | Name in model | Value used |
|---|---:|---:|
| Input voltage | `V_in` | **12 V** |
| Positive Output voltage | `Vo_SEPIC` | **+15 V / 6.67 A** |
| Negative Output voltage | `Vo_CUK` | **-15 V / 3.87 A** |
| Switching frequency | `f_s` | **100 kHz** |
| Input inductors (SEPIC, per phase) | `L_i1, L_i2` | **3.0 mH** (avg. current 2.08 A each) |
| Coupling capacitors (SEPIC) | `C_i1, C_i2` | **180 μF** (12 V avg. across each) |
| Output inductors (SEPIC) | `L_o1, L_o2` | **10.0 mH** (avg. current 1.67 A each) |
| Output capacitor (SEPIC) | `C_o` | **250 μF** (15 V avg.) |
| Input inductors (Cuk, per phase) | `L_i3, L_i4` | **4.7 mH** (avg. current 1.21 A each) |
| Transfer capacitors (Cuk) | `C_i3, C_i4` | **39 μF** (27 V avg. each) |
| Output inductors (Cuk) | `L_o3, L_o4` | **12.0 mH** (avg. current 0.97 A each) |
| Output capacitor (Cuk) | `C_o1` | **250 μF** (-15 V avg.) |
| Main input current (DC Stage) | `I_in` | **13.16 A (total drawn)** |

---

## Waveform Results

| Filename | Description | Peak Value (Max) | Average (DC) | Ripple |
|---|---|---:|---:|---:|
| `Images/I1_waveform.png` | SEPIC Inductor Current (Phase 1) | ~2.5 A | ~2.08 A | small |
| `Images/I2_waveform.png` | SEPIC Inductor Current (Phase 2) | ~2.5 A | ~2.08 A | small |
| `Images/I3_waveform.png` | Cuk Inductor Current (Phase 3) | ~1.5 A | ~1.21 A | small |
| `Images/I4_waveform.png` | Cuk Inductor Current (Phase 4) | ~1.5 A | ~1.21 A | small |
| `Images/I_battery_waveform.png` | Total Input Current | ~14 A | ~13.16 A | small |
| `Images/I_load_waveform.png` | Combined Load Current | — | ~10.54 A total (≈6.67 A + 3.87 A) | small |
| `Images/Vo_plus_waveform.png` | Positive Output Voltage | ~15.2 V | ~15 V | ~0.36 V |
| `Images/Vo_minus_waveform.png` | Negative Output Voltage | -14.8 V | ~-15 V | ~0.36 V |

---

## Design / scaling notes (100 kHz vs 5 kHz)
The model components are sized for **100 kHz operation**.  
If you want to run the same converter at **5 kHz**, inductance and capacitance must be increased to keep ripple levels similar:

- Scaling factor = 100 kHz / 5 kHz = 20.  
- Example (SEPIC): L ≈ 3.0 mH × 20 = **60 mH**, C ≈ 180 μF × 20 = **3600 μF**.  
- Example (Cuk): L ≈ 4.7 mH × 20 = **94 mH**, C ≈ 39 μF × 20 = **780 μF**.  

---

## Device selection & stress checks
- **MOSFETs:** Voltage ≥ (V_in + |V_out|), current ≥ max inductor current.  
- **Diodes:** Reverse-block ≥ (V_in + |V_out|), current ≥ peak inductor current.  
- **Inductors:** Must handle DC + ripple current safely (SEPIC ~2 A, Cuk ~1.2 A).  
- **Capacitors:** Low ESR, handle up to ~27 V across transfer caps and ~0.36 V ripple at outputs.  

---

## Known issues
- Running at 5 kHz without scaling L/C will cause **excessive ripple**.  
- Device models are generic — for hardware implementation, select parts with adequate ratings.  

---

## Author / Contact
- Author: *Your Name*  
- Contact: *your.email@domain*  

---

## License
MIT License
