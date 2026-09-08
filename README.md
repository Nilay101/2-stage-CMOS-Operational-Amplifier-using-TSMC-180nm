# Two-Stage CMOS Operational Amplifier — 180 nm

A transistor-level implementation and simulation study of a **two-stage CMOS operational amplifier** designed using a 180 nm CMOS process. The design focuses on achieving a practical combination of **high open-loop gain, adequate bandwidth, stable closed-loop operation, controlled slew rate, and low power consumption**.

The complete design flow includes first-order analytical sizing followed by transistor-level verification in **LTspice** using BSIM3 Level 49 device models.

---

## Project Highlights

* Two-stage CMOS operational amplifier architecture
* 180 nm CMOS technology
* 1.8 V single supply
* Differential input stage with active PMOS load
* Common-source second gain stage
* Miller frequency compensation
* 2 pF capacitive load
* Open-loop gain above 68 dB
* Unity-gain bandwidth above 30 MHz
* Phase margin above 60°
* Slew rate of approximately 28 V/µs
* Power consumption below 300 µW
* Transistor dimensions determined using analytical design equations
* Final circuit verified through LTspice AC and transient simulations

---

## Circuit Architecture

The amplifier consists of two cascaded gain stages.

### Stage 1 — Differential Gain Stage

The input section uses an NMOS differential pair with a PMOS current-mirror active load. This stage converts the differential input voltage into a single-ended signal while providing the initial voltage gain.

A tail current source establishes the operating current of the differential pair and also influences the input common-mode range.

### Stage 2 — Voltage Gain Stage

The output of the first stage drives a common-source second stage. The second stage provides additional voltage gain and drives the external capacitive load.

### Frequency Compensation

A Miller capacitor is connected between the first- and second-stage high-impedance nodes. This introduces pole splitting, moving the dominant pole to a lower frequency and pushing the non-dominant pole to a higher frequency to obtain sufficient phase margin.

---

## Transistor-Level Schematic

![Two-Stage CMOS Op-Amp Schematic](https://github.com/Nilay101/2-stage-CMOS-Operational-Amplifier-using-TSMC-180nm/blob/8f856c964e84322b60da62ed8a50cbd44d4179bd/Images/opamp_circuit.png)

*Figure 1 — Transistor-level implementation of the compensated two-stage CMOS operational amplifier.*

---

# Design Requirements

The amplifier was sized around the following primary targets:

| Parameter                 | Design Target |
| ------------------------- | ------------: |
| Technology                |        180 nm |
| Supply Voltage            |         1.8 V |
| Minimum DC Gain           |         60 dB |
| Minimum GBW               |        30 MHz |
| Minimum Phase Margin      |           60° |
| Minimum Slew Rate         |       20 V/µs |
| Load Capacitance          |          2 pF |
| Maximum Power Dissipation |        300 µW |

The transistor dimensions were initially estimated using long-channel MOS equations and subsequently evaluated using the selected SPICE device models.

---

# Final Simulation Performance

The final simulated performance is summarized below.

| Performance Metric | ICMR+ Condition | ICMR− Condition |
| ------------------ | --------------: | --------------: |
| Technology         |          180 nm |          180 nm |
| Supply Voltage     |           1.8 V |           1.8 V |
| DC Gain            |           70 dB |           68 dB |
| Gain-Bandwidth     |          35 MHz |          31 MHz |
| Phase Margin       |             61° |             65° |
| Slew Rate          |         28 V/µs |         28 V/µs |
| Load Capacitance   |            2 pF |            2 pF |
| Power Dissipation  |        < 300 µW |        < 300 µW |

The results indicate that the amplifier meets the major gain, bandwidth, stability, slew-rate, and power requirements across the evaluated input common-mode conditions.

---

# AC Analysis

The frequency response was evaluated using small-signal AC analysis.

The simulated response demonstrates:

* Open-loop gain greater than 68 dB
* Unity-gain crossover above 30 MHz
* Phase margin greater than 60°
* Stable frequency response under the evaluated operating conditions

![AC Frequency Response](https://github.com/Nilay101/2-stage-CMOS-Operational-Amplifier-using-TSMC-180nm/blob/0c627e89c1676f51f423bb5c631afdf741973f50/Images/waveform.png)

*Figure 2 — Simulated open-loop frequency response showing gain and phase characteristics.*

---

# Design Methodology

The circuit sizing was divided into several design stages.

1. Establish the compensation and slew-rate requirements.
2. Determine the required input-stage transconductance from the GBW target.
3. Calculate the differential-pair dimensions.
4. Size the PMOS active load according to the upper input common-mode constraint.
5. Determine the tail transistor dimensions from the lower input common-mode requirement.
6. Increase the second-stage transconductance to maintain adequate frequency separation.
7. Verify the resulting design using transistor-level SPICE simulations.

The analytical sizing was performed before evaluating the design with the BSIM3 Level 49 models.

---

# Hand Calculations

## 1. Compensation Capacitor ($C_c$) & Slew Rate (M5)

To secure a 60° Phase Margin, the secondary pole must be pushed past the 0 dB crossing.

\(C_c \ge 0.22 C_L\)

\(C_c \ge 0.22 \times 2\text{ pF} = 440\text{ fF}\)

* **Selected $C_c$:** 800 fF (To guarantee stability margin).

Slew rate requirements dictate the tail current:

\(I_5 = \text{SR} \times C_c\)

\(I_5 = (20\text{ V/µs}) \times 800\text{ fF} = 16\text{ µA}\)

* **Selected Tail Current ($I_5$):** 20 µA.
* **Branch Current ($I_D$):** 10 µA per branch.

---

## 2. Differential Input Pair (M1, M2)

The required transconductance ($g_{m1}$) for a 30 MHz GBW is:

\(g_{m1} = \text{GBW} \times C_c \times 2\pi\)

\(g_{m1} = 30\text{ MHz} \times 800\text{ fF} \times 2\pi \approx 150\text{ µS} \rightarrow \mathbf{160\text{ µS}}\)

Calculating the aspect ratio using $µ_n C_{ox} = 207\text{ µA/V}^2$:

$$
(\frac{W}{L})_{1,2}
=
\frac{g_{m1}^2}
{µ_n C_{ox} (2 I_D)}
$$

$$
(\frac{W}{L})_{1,2}
=
\frac{(160\text{ µS})^2}
{207\text{ µA/V}^2 \times 20\text{ µA}}
\approx
\mathbf{6.18}
$$

---

## 3. Active Load (M3, M4) & ICMR+

To guarantee survival at the 1.6 V Input Common-Mode Range (ICMR) maximum, the PMOS threshold voltage ($V_{T3}$) was extracted as 0.3906 V, and the NMOS threshold ($V_{T1}$) was assumed to shift to 0.3862 V due to the body effect.

$$
(\frac{W}{L})_{3,4}
=
\frac{2 I_{D3}}
{µ_p C_{ox}
[V_{DD} - \text{ICMR+} - |V_{T3}|_{max} + V_{T1(min)}]^2}
$$

Using $µ_p C_{ox} = 55\text{ µA/V}^2$:

$$
(\frac{W}{L})_{3,4}
=
\mathbf{9.5}
$$

---

## 4. Tail Current Mirror (M5) & ICMR−

To keep M5 out of the triode region at the 0.8 V ICMR limit, the worst-case maximum threshold voltage shift ($V_{T1(max)} = 0.49\text{ V}$) was utilized.

$$
V_{DSAT5}
\le
\text{ICMR-} - V_{OV1} - V_{T1(max)}
$$

$$
V_{DSAT5}
\le
0.8\text{ V} - 0.125\text{ V} - 0.49\text{ V}
=
\mathbf{184\text{ mV}}
$$

$$
(\frac{W}{L})_5
=
\frac{2 I_{D5}}
{µ_n C_{ox} (V_{DSAT5})^2}
$$

$$
(\frac{W}{L})_5
=
\mathbf{5.7}
$$

---

## 5. Second Stage (M6, M7)

To ensure the second pole does not degrade the phase margin, $g_{m6}$ must be significantly larger than $g_{m1}$.

$$
g_{m6} \ge 10 \times g_{m1}
\rightarrow
\mathbf{1600\text{ µS}}
$$

Scaling from M4 to achieve this massive transconductance:

$$
(\frac{W}{L})_6
=
\mathbf{149}
$$

$$
(\frac{W}{L})_7
=
\frac{I_7}{I_5}
(\frac{W}{L})_5
=
\mathbf{45}
$$

---

# Simulation Approach

The analytical transistor dimensions were implemented in LTspice using the corresponding 180 nm MOSFET models.

The design was then evaluated through:

### DC Operating-Point Analysis

Used to verify:

* Drain currents
* Device operating regions
* Bias voltages
* Overdrive voltages
* Quiescent power consumption

### AC Analysis

Used to extract:

* DC open-loop gain
* Unity-gain frequency
* Gain-bandwidth product
* Phase margin
* Frequency-dependent gain

### Transient Analysis

Used to evaluate:

* Slew-rate behavior
* Large-signal response
* Output settling behavior
* Capacitive-load response

---

# Key Design Trade-offs

One of the major challenges in this design was balancing **speed, stability, gain, and power consumption**.

Increasing the compensation capacitor improves pole separation and can make the amplifier easier to stabilize, but it also increases the current required to achieve a given slew rate.

Similarly, increasing the second-stage transconductance helps push the non-dominant pole to a higher frequency, improving phase margin. However, this requires larger transistor dimensions and can increase parasitic capacitances and power consumption.

The final design therefore represents a compromise between:

**Gain ↔ Bandwidth ↔ Stability ↔ Slew Rate ↔ Power**

---

# Results at a Glance

| Requirement  |    Target | Simulation |
| ------------ | --------: | ---------: |
| DC Gain      |   ≥ 60 dB |   68–70 dB |
| GBW          |  ≥ 30 MHz |  31–35 MHz |
| Phase Margin |     ≥ 60° |     61–65° |
| Slew Rate    | ≥ 20 V/µs |    28 V/µs |
| Load         |      2 pF |       2 pF |
| Power        |  ≤ 300 µW |   < 300 µW |
| Supply       |     1.8 V |      1.8 V |

---

# Tools & Technologies

**Design & Simulation**

* LTspice
* SPICE / BSIM3 Level 49 models
* 180 nm CMOS technology

**Core Concepts**

* CMOS analog circuit design
* Differential amplifiers
* Current mirrors
* Active loads
* Common-source amplifiers
* Miller compensation
* Frequency response
* Phase-margin analysis
* Slew-rate analysis
* Input common-mode range
* MOSFET biasing and sizing

---

# Repository Structure

```text
2-stage-CMOS-Operational-Amplifier/
│
├── Images/
│   ├── opamp_circuit.png
│   └── waveform.png
│
├── LTspice/
│   ├── schematic/
│   ├── models/
│   └── simulations/
│
├── Calculations/
│   └── design_calculations.pdf
│
└── README.md
```

---

# Conclusion

A complete two-stage CMOS operational amplifier was designed for a **1.8 V, 180 nm CMOS environment** using analytical transistor sizing followed by SPICE-based verification.

The final implementation achieves an open-loop gain of approximately **68–70 dB**, a **31–35 MHz gain-bandwidth product**, phase margin above **60°**, and a simulated slew rate of **28 V/µs**, while maintaining power dissipation below **300 µW** with a **2 pF load**.

The project provided practical exposure to the complete analog IC design cycle, from **MOSFET-level hand calculations and bias-point selection to frequency compensation and transistor-level simulation**.
