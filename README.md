# 2-Stage CMOS Operational Amplifier Design (180nm)

This repository contains the hand calculations and design methodology for a two-stage CMOS operational amplifier designed in a 180nm process. The design is optimized to meet a specified gain-bandwidth product (GBW), phase margin, and slew rate while adhering to a strict power budget.

## Target Specifications

The target specifications for this design, based on `image_127ca5.png`, are as follows:

| Parameter | Target Specification |
| :--- | :--- |
| **Technology** | 180nm |
| **Supply Voltage (VDD)** | 1.8 V |
| **DC Gain** | $\geq$ 60 dB (1000 V/V) |
| **Gain-Bandwidth (GBW)** | $\geq$ 30 MHz |
| **Phase Margin (PM)** | $\geq$ 60° |
| **Slew Rate (SR)** | $\geq$ 20 V/µs |
| **Load Capacitance (CL)** | 2 pF |
| **Power Dissipation** | $\leq$ 300 µW |

---

## 1. Process Assumptions & Topology Selection
For the hand calculations, we assume typical 180nm process parameters. *Note: These should be replaced with the exact parameters from your specific foundry PDK during simulation.*

*   $\mu_n C_{ox} \approx 200 \, \mu A/V^2$
*   $\mu_p C_{ox} \approx 50 \, \mu A/V^2$
*   $V_{THN} \approx 0.45 \, V$, $|V_{THP}| \approx 0.5 \, V$
*   Channel length modulation parameter $\lambda \approx 0.1 \, V^{-1}$ (Assuming $L = 0.36 \mu m$ to achieve high gain)

**Topology:** Unbuffered 2-stage CMOS op-amp with an NMOS differential input pair, a PMOS active load, a PMOS second-stage common-source amplifier, and Miller compensation ($C_c$).

---

## 2. Step-by-Step Design Calculations

### Step 1: Total Power & Current Budget
The maximum power dissipation is $300 \, \mu W$ from a $1.8 \, V$ supply.
$$I_{total(max)} = \frac{P_{diss}}{V_{DD}} = \frac{300 \, \mu W}{1.8 \, V} = 166.67 \, \mu A$$
We will split this current between the first stage ($I_5$), the second stage ($I_6$), and the bias network.

### Step 2: Compensation Capacitor ($C_c$)
To achieve a Phase Margin of $\geq 60^\circ$, we place the non-dominant pole at roughly $2.2 \times \omega_{GBW}$. Typically, $C_c$ is chosen based on the load capacitance to ensure stability.
Let's choose $C_c = 0.5 \times C_L$:
$$C_c = 0.5 \times 2 \, \text{pF} = 1 \, \text{pF}$$

### Step 3: First Stage Tail Current ($I_5$) based on Slew Rate
The Slew Rate is limited by the rate at which the tail current can charge the compensation capacitor.
$$SR = \frac{I_5}{C_c} \implies I_5 = SR \times C_c$$
$$I_5 = (20 \times 10^6 \, \text{V/s}) \times (1 \times 10^{-12} \, \text{F}) = 20 \, \mu A$$
To provide a safety margin for parasitic capacitances, we choose **$I_5 = 30 \, \mu A$**.
Therefore, the current in each branch of the differential pair is **$I_1 = I_2 = 15 \, \mu A$**.

### Step 4: Sizing the Input Differential Pair (M1, M2)
The GBW is determined by the transconductance of the input pair ($g_{m1}$) and $C_c$.
$$\omega_{GBW} = \frac{g_{m1}}{C_c} \implies g_{m1} = 2 \pi \times GBW \times C_c$$
$$g_{m1} = 2 \pi \times (30 \times 10^6) \times (1 \times 10^{-12}) \approx 188.5 \, \mu A/V$$
Using the square-law equation for transconductance:
$$g_{m1} = \sqrt{2 \mu_n C_{ox} (W/L)_1 I_{D1}}$$
$$188.5 \times 10^{-6} = \sqrt{2 \times (200 \times 10^{-6}) \times (W/L)_1 \times (15 \times 10^{-6})}$$
$$3.55 \times 10^{-8} = 6 \times 10^{-9} \times (W/L)_1 \implies (W/L)_1 = (W/L)_2 \approx 5.92 \rightarrow \mathbf{6}$$

### Step 5: Sizing the First Stage Active Load (M3, M4)
To minimize noise and offset, we choose a reasonable overdrive voltage for the PMOS load, e.g., $|V_{ov3}| \approx 0.2 \, V$.
$$(W/L)_3 = \frac{2 I_{D3}}{\mu_p C_{ox} V_{ov3}^2} = \frac{2 \times 15 \, \mu A}{50 \, \mu A/V^2 \times (0.2 \, V)^2} = \frac{30}{2} = \mathbf{15}$$
So, $(W/L)_3 = (W/L)_4 = 15$.

### Step 6: Second Stage Transconductance ($g_{m6}$) and Current ($I_6$)
To guarantee a Phase Margin $\geq 60^\circ$, the non-dominant pole ($p_2$) must be placed past the GBW frequency.
$$p_2 \approx \frac{g_{m6}}{C_L} \geq 2.2 \times \omega_{GBW}$$
$$g_{m6} \geq 2.2 \times (188.5 \, \mu A/V) \times \left(\frac{2 \, \text{pF}}{1 \, \text{pF}}\right)$$
Wait, $p_2$ actual relationship: $g_{m6} \geq 2.2 \times \omega_{GBW} \times C_L = 2.2 \times (188.5 \times 10^6 \, \text{rad/s}) \times 2 \times 10^{-12} \, \text{F} \approx 829.4 \, \mu A/V$.
Let's design for **$g_{m6} = 1000 \, \mu A/V$** ($1 \, mA/V$) for margin.

Assuming a typical overdrive voltage of $V_{ov6} = 0.2 \, V$:
$$I_6 = \frac{g_{m6} \times V_{ov6}}{2} = \frac{1000 \, \mu A/V \times 0.2 \, V}{2} = \mathbf{100 \, \mu A}$$
Total current so far = $I_5 + I_6 = 30 \, \mu A + 100 \, \mu A = 130 \, \mu A$ (Well under the $166.67 \, \mu A$ limit).

### Step 7: Sizing the Second Stage Amplifier (M6)
Using $I_6$ and $g_{m6}$:
$$(W/L)_6 = \frac{g_{m6}^2}{2 \mu_p C_{ox} I_6} = \frac{(1000 \times 10^{-6})^2}{2 \times (50 \times 10^{-6}) \times (100 \times 10^{-6})} = \frac{10^{-6}}{10^{-8}} = \mathbf{100}$$

### Step 8: Sizing the Current Mirrors (M5, M7)
Assuming a $0.2 \, V$ overdrive voltage for the NMOS current mirrors:
$$(W/L)_5 = \frac{2 I_5}{\mu_n C_{ox} V_{ov}^2} = \frac{2 \times 30 \, \mu A}{200 \, \mu A/V^2 \times (0.2)^2} = \frac{60}{8} = \mathbf{7.5}$$
$$(W/L)_7 = \frac{2 I_6}{\mu_n C_{ox} V_{ov}^2} = \frac{2 \times 100 \, \mu A}{200 \, \mu A/V^2 \times (0.2)^2} = \frac{200}{8} = \mathbf{25}$$

### Step 9: DC Gain Check
The total DC gain is $A_v = A_{v1} \times A_{v2}$.
$$A_{v1} = g_{m1} (r_{o2} || r_{o4}) = g_{m1} \left( \frac{1}{\lambda_n I_{D2}} || \frac{1}{\lambda_p I_{D4}} \right)$$
Assuming $\lambda_n \approx \lambda_p \approx 0.1 \, V^{-1}$:
$$r_{o2} = r_{o4} = \frac{1}{0.1 \times 15 \, \mu A} = 666.7 \, k\Omega$$
$$A_{v1} = 188.5 \, \mu A/V \times (333.3 \, k\Omega) \approx 62.8 \, V/V$$
$$A_{v2} = g_{m6} (r_{o6} || r_{o7}) = g_{m6} \left( \frac{1}{\lambda_p I_6} || \frac{1}{\lambda_n I_6} \right)$$
$$r_{o6} = r_{o7} = \frac{1}{0.1 \times 100 \, \mu A} = 100 \, k\Omega$$
$$A_{v2} = 1000 \, \mu A/V \times (50 \, k\Omega) = 50 \, V/V$$
$$A_{v(total)} = 62.8 \times 50 = \mathbf{3140 \, V/V}$$
$3140 \, V/V$ equals approximately **$69.9 \, dB$**, safely exceeding the $60 \, dB$ specification.

---

## 3. Final Aspect Ratios Summary

Assuming a channel length of $L = 0.36 \, \mu m$ to preserve high output resistance:

| Transistor | Role | $W/L$ Ratio | Calculated Width ($W$) for $L=0.36 \mu m$ |
| :--- | :--- | :--- | :--- |
| **M1, M2** | Input Differential Pair (NMOS) | $6$ | $2.16 \, \mu m$ |
| **M3, M4** | First Stage Active Load (PMOS) | $15$ | $5.4 \, \mu m$ |
| **M5** | First Stage Tail Current (NMOS) | $7.5$ | $2.7 \, \mu m$ |
| **M6** | Second Stage Amplifier (PMOS) | $100$ | $36.0 \, \mu m$ |
| **M7** | Second Stage Current Source (NMOS)| $25$ | $9.0 \, \mu m$ |
| **$C_c$** | Compensation Capacitor | N/A | $1.0 \, \text{pF}$ |
| **$R_z$** | Nulling Resistor (Optional) | N/A | $\approx 1 / g_{m6} = 1 \, k\Omega$ |

## 4. Next Steps
1. Create the schematic in Cadence Virtuoso or equivalent SPICE tool using the actual PDK models.
2. Run DC operating point analysis to ensure all transistors are in the saturation region.
3. Run AC simulation to verify Gain, GBW, and Phase Margin.
4. Run Transient simulation with a step input to verify Slew Rate.
5. Iteratively tweak W/L ratios to account for short-channel effects and parasitic capacitances not captured in hand calculations.
