# 4-Phase Interleaved Cuk-SEPIC Converter — Simulink Model

## Overview
This repository contains a Simulink model of a **4-phase interleaved Cuk-SEPIC** DC–DC converter designed to boost 12 V to 30 V using four interleaved power stages. The model includes switching MOSFETs, input inductors, coupling capacitors, output filter, diodes, control (PWM generation / gating), and measurement blocks. The project also contains waveform screenshots produced from the simulation.

The model was built at a switching frequency of **100 kHz** (see “Notes” below). A user originally requested 5 kHz, but the model’s component values match a 100 kHz design — that mismatch is the main cause of any L/C discrepancy if you try to use it at 5 kHz.

---

## Repository contents
- `model/` — (Simulink files) the full Simulink model files (.slx/.mdl).  
- `figures/` — waveform images exported from the simulation:
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

> If your repo uses a different folder layout, adjust the paths above accordingly.

---

## Model parameters (as used in this Simulink model)
| Component / Parameter | Name in model | Value used |
|---|---:|---:|
| Input voltage | `V_in` | **12 V** |
| Output voltage (target) | `V_out` | **30 V** |
| Load resistor | `R_load` | **27 Ω** |
| Switching frequency | `f_s` | **100 kHz** |
| Duty cycle | `D` | **~71.4% (implicit — set by control loop to reach 30 V)** |
| Input inductors (per phase) | `L_11, L_12, L_13, L_14` | **137 μH** |
| Coupling / switching capacitors | `C_11, C_21, etc.` | **22 μF** |
| MOSFET & diode stresses | `V_ds`, `I_d(peak)`, `V_r(max)`, `I_f(peak)` | **Not specified in model (N/A)** — select devices with adequate voltage/current margin (see notes) |

**Notes from the model spreadsheet:**
- The model was built for **100 kHz**. The user requested 5 kHz; because L & C scale with switching frequency, the component values are too low for 5 kHz operation and will cause high ripple at the lower frequency.
- The shown `L` and `C` are too low for 5 kHz and will result in excessive current and voltage ripple if you simply drop the switching frequency.
- MOSFETs/Diodes: the model does not explicitly list device voltage/current ratings — you must pick real parts rated to withstand `V_in + V_out` when off and peak inductor currents during conduction/transient conditions.

---

## How to run the model (Simulink)
1. Open MATLAB and Simulink.
2. Open the model file located in `model/` (e.g. `4phase_cuk_sepic.slx`).
3. Confirm sample time and solver:
   - Discrete sample time used in the model block appears as `1e-07` (consistent with a 100 kHz switching period; check sample settings).
   - Solver: `Fixed-step` recommended for switching simulations; choose small step size (≤ switching period / 20). The model uses discrete switching blocks and a small fixed time step.
4. Run the simulation for a few milliseconds (several switching cycles; e.g. 2–10 ms) to capture steady waveforms.
5. Export waveforms or open scopes and save the figures to `figures/` if needed.

---

## Figure list and textual comments (captions / explanations)
Below are the figures saved from simulation and textual comments you can use as captions in a paper/README/figures list.

### `I1_waveform.png`  
**Caption / comment:** Phase-1 inductor current (I1). Shows the inductor conduction during switching intervals and the ripple current superimposed on the average inductor current. Interleaving with other phases reduces total input current ripple.

### `I2_waveform.png`  
**Caption / comment:** Phase-2 inductor current (I2). Similar waveform to I1 but phase-shifted by 90° (for four-phase interleaving) to spread switching events and reduce input and output ripple.

### `I3_waveform.png`  
**Caption / comment:** Phase-3 inductor current (I3). Phase-shifted inductor current showing the same ripple and average as other phases; used to demonstrate balanced current sharing between phases.

### `I4_waveform.png`  
**Caption / comment:** Phase-4 inductor current (I4). Completes the 4-phase interleaving pattern; combined with other phases, the net input current ripple is significantly reduced.

### `I_battery_waveform.png`  
**Caption / comment:** Battery / source current waveform. Shows the total current drawn from the input source (sum of all phase currents). Because of interleaving, the battery current ripple is lower than the ripple of a single phase.

### `I_load_waveform.png`  
**Caption / comment:** Load current waveform. Shows current delivered to the load (at ~30 V out / 27 Ω load). Check steady-state magnitude and any ripple due to converter switching.

### `Vo_plus_waveform.png`  
**Caption / comment:** Output positive rail voltage (`V_o_plus`). Shows the DC output voltage and switching ripple present on the positive output node. Should be ~30 V DC with small ripple for 100 kHz design and the selected component values.

### `Vo_minus_waveform.png`  
**Caption / comment:** Output negative rail / reference (`V_o_minus`). If used as negative rail or return measurement, shows the voltage relative to ground. Use this to check output regulation and common-mode behavior.

> Tip: Add time and amplitude annotations to each saved figure (MATLAB/Simulink scope settings) for publication clarity.

---

## Design / scaling notes (100 kHz vs 5 kHz)
The model components are sized for 100 kHz operation. If you want to run the same converter at **5 kHz**, inductance and capacitance must be increased to keep ripple levels similar. Inductance scales approximately inversely with switching frequency (for a given ripple specification) and so does capacitance (for the same ripple and ESR assumptions).

**Scaling factor calculation (digit-by-digit):**
- Ratio = (100 kHz) / (5 kHz) = `100000 / 5000` = `20`.
- New L (approx) = `137 µH * 20` = `137 * 20 = 2740 µH = 2740 µH = 2.74 mH`.
- New C (approx) = `22 µF * 20` = `22 * 20 = 440 µF`.

**So:** For ~5 kHz operation (keeping similar ripple targets), try:
- `L` per phase ≈ **2.74 mH** (≈ 2740 µH)  
- `C` (filter/coupling) ≈ **440 µF**

These are approximate — proper design requires ripple targets, ESR, core selection (for inductors), and thermal/current saturation checks.

---

## Device selection & stress checks
- **MOSFETs:** Choose MOSFETs with `V_ds` rating ≥ `V_in + V_out` (peak transient margin recommended). Also check continuous and pulsed current ratings exceed expected peak inductor currents (I_peak).
- **Diodes:** Select diodes (or synchronous rectifiers) that can block `V_in + V_out` when reverse biased and handle the peak inductor current when the switch is off.
- **Inductors:** Check core saturation current and RMS/peak current capability. Interleaving reduces per-phase RMS current but ensure each inductor’s saturation current > inductor peak.
- **Capacitors:** Low-ESR electrolytic / film capacitors on the output and coupling capacitors to reduce ripple and heating.

---

## Known issues & recommendations
- The model spreadsheet notes the design mismatch: the user requested a 5 kHz design but values correspond to **100 kHz**. If you change `f_s` to 5 kHz without changing L/C, you will see much larger voltage/current ripple and possibly simulation instability.
- The model does not list MOSFET/diode device part numbers or ratings — pick parts with comfortable margins (e.g., MOSFET `V_ds` ≥ 100 V if `V_in + V_out` ~ 42 V plus transients).
- Use measurement probes and currents to verify power balance: input power vs. output power plus losses.
- For long transient runs, use solver settings appropriate for switching circuits (fixed step, sufficiently small step, or use averaged model for faster long-term studies).

---

## Useful additions / future work
- Add explicit MOSFET/diode part models for thermal & conduction loss estimation.
- Add an averaged model (for control design & slower simulations).
- Add a closed-loop control block for output voltage regulation (PID/PI with current limit).
- Create parameterized script to auto-scale L and C when switching frequency or ripple targets are changed.

---

## Licensing
This repository is released under the MIT License — adapt as you prefer.

---

## Author / Contact
- Author: *Your name here*  
- Contact: *your.email@domain* (edit as needed)

---

## Citation
If you use this model in academic work, please cite the repository and include simulation details (switching frequency, component values, and interleaving strategy).

