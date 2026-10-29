# Ep10 – Designing a Battery Charge Status Monitoring Circuit

**Course:** Professional Electronic Design Course – Level 1 (Simple Electronic Design)
**Source:** Waleed Issa Academy – [تصميم دائرة مراقبة حالة شحن البطارية (Designing a battery charge status monitoring circuit)](https://www.youtube.com/watch?v=ut-fORok8qY&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=148) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

**The task:** a 12 V battery (car, motorbike, machine…) powers some device. Add a small **charge indicator** fed from the same battery:

| Battery voltage | LED |
|---|---|
| ≥ 10 V (OK) | **Green** only |
| < 10 V (needs charging) | **Red** only |

The episode first introduces a very common building block, the **inverting transistor pair** (two transistors where only one is ever on), and then turns it into the battery monitor by adding a single **Zener diode**. The second half is about *design*: how to pick each resistor value from the currents each branch needs.

**Prerequisites:** how a transistor switches on (saturation) and off (cut-off), and how a Zener diode conducts once its breakdown voltage is reached.

---

## 1. Warm-up: a Saturated Transistor Pulls Its Collector to 0 V

Take an NPN transistor with a 10 kΩ resistor from +12 V to its collector, and a push switch feeding its base through a base resistor.

| Switch | Transistor | Collector voltage |
|---|---|---|
| Open | Cut-off (acts like an open circuit) | **≈ 12 V** |
| Closed | Saturated (C–E almost a short) | **≈ 0 V** |

- **Switch open:** no current flows through the 10 kΩ, so it drops nothing ($V = I \times R = 0$). The collector sits at the full 12 V.
- **Switch closed:** the transistor connects the collector to ground. All 12 V now drops across the 10 kΩ and the collector reads only a few µV–mV in the simulator.

So the collector is a **logic output**: 12 V or 0 V depending on the switch. The 10 kΩ can be replaced by any load (an LED + resistor, a relay…) and the same thing happens: the load turns on when the transistor saturates.

---

## 2. The Inverting Transistor Pair

Now feed that collector (12 V or 0 V) into the base of a **second** transistor through a large resistor:

**Connections:**

- **Q1 collector:** $+12\ \text{V} \rightarrow 1\ \text{k}\Omega \rightarrow \text{green LED} \rightarrow C_{Q1}$
- **Q2 base:** $C_{Q1} \rightarrow R_{B2}\ (\approx 46\ \text{k}\Omega) \rightarrow B_{Q2}$
- **Q2 collector:** $+12\ \text{V} \rightarrow 1\ \text{k}\Omega \rightarrow \text{red LED} \rightarrow C_{Q2}$
- **Q1 base:** input (switch, or later the Zener) $\rightarrow R_{B1} \rightarrow B_{Q1}$
- Both emitters to ground.

- **Q1 off** → its collector is high → current flows through the green LED's 1 kΩ and $R_{B2}$ into Q2's base → **Q2 on → red LED lit**.
- **Q1 on** → its collector is pulled to ≈ 0 V → Q2's base gets nothing → **Q2 off → red off, green lit** (the green LED now has a path to ground through Q1).

Only one transistor (and one LED) is on at any time. Whatever drives Q1's base decides which colour lights.

### Why doesn't the green LED glow when Q1 is off?

Q2's base current does flow through the green LED, but $R_{B2}$ (≈ 46 kΩ) limits it to about **226 µA** (value shown in the video). An LED needs roughly 5–20 mA to light properly, so it stays dark. 226 µA is, however, plenty to switch Q2 on, because a transistor's base needs only microamps.

---

## 3. From Switch to Battery Monitor: Add a Zener

Replace the push switch with something that conducts **only when the battery is above 10 V**: a **10 V Zener diode** in series with Q1's base resistor (cathode toward the battery).

For current to reach Q1's base, the battery must overcome everything in that path:

$$V_{bat} > V_Z + I_{B1} R_{B1} + V_{BE} \approx 10 + 0.3 + 0.7 \approx 11\ \text{V}$$

| Battery | Zener | Q1 | Q2 | LED |
|---|---|---|---|---|
| 12 V, 11 V | Conducts | On | Off | **Green** |
| ≤ 10 V | Blocks (below breakdown) | Off | On | **Red** |

The Zener is the "smart" part: it is the element that makes the decision.

---

## 4. Designing the Resistor Values

"Designing" a circuit means choosing component values from the currents each branch must carry. Start from the load and work backwards.

### Step 1: LED resistors

Each LED gets **1 kΩ**. Rule of thumb from the instructor: **220 Ω for a 5 V supply, 1 kΩ for anything above 5 V** (10–15 V). This gives roughly 10 mA, which is bright enough. 220 Ω at 12 V would also work, just brighter than needed.

### Step 2: Transistor and β

Transistor: **BC547B**. It easily handles 10 mA. From the datasheet, the "B" grade has $h_{FE} = \beta = 200$–$450$. If you can't measure β with a multimeter, design with the **minimum (200)**, the worst case.

$$I_{B} = \frac{I_C}{\beta} = \frac{10\ \text{mA}}{200} = 50\ \mu\text{A}$$

To be sure the transistor is **saturated**, multiply the base current by 2–10 (rule from the Practical Electronics course).

### Step 3: Q1's base resistor $R_{B1}$ (Zener branch)

The base current also flows through the Zener, so it must be enough to keep the Zener in breakdown. The **BZX85C10** datasheet:

- $V_Z = 9.4$–$10.6\ \text{V}$
- Test currents: 25 mA and **0.5 mA** (a graph also shows operation down to ≈ 0.1 mA)
- No $I_{Z,min}$ is given, so the lowest tested current, **0.5 mA**, is taken as the minimum.

Conveniently, $50\ \mu\text{A} \times 10 = 0.5\ \text{mA}$, so one value satisfies both the Zener and the saturation rule: $I_{B1} = 0.5\ \text{mA}$.

KVL around the base loop, at the lowest battery voltage where green should still be on (≈ 11 V):

$$11 - \underbrace{10}_{V_Z} - I_{B1} R_{B1} - \underbrace{0.7}_{V_{BE}} = 0
\quad\Rightarrow\quad
R_{B1} = \frac{0.3\ \text{V}}{0.5\ \text{mA}} = 600\ \Omega$$

Used in the build: **677 Ω** (nearest available, ≈ 680 Ω standard).

### Step 4: Q2's base resistor $R_{B2}$

When the battery drops to 10 V, Q1 is off and Q2 is fed through the 1 kΩ (+ green LED) and $R_{B2}$. The current must be **enough for Q2 but too small to light the green LED**.

At $I_{B2} = 50\ \mu\text{A}$ (ignoring the tiny LED drop):

$$10 - 0.7 = I_{B2}(1\ \text{k}\Omega + R_{B2}) \quad\Rightarrow\quad R_{B2} \approx \frac{9.3\ \text{V}}{50\ \mu\text{A}} \approx 186\ \text{k}\Omega$$

That is too large to drive Q2 reliably, so the current is multiplied by 4 → $I_{B2} = 200\ \mu\text{A}$:

$$R_{B2} \approx \frac{9.3\ \text{V}}{200\ \mu\text{A}} \approx 46.5\ \text{k}\Omega \;\rightarrow\; \textbf{46 kΩ}\ (\approx 47\ \text{k}\Omega\ \text{standard})$$

200 µA saturates Q2 but is still about 50× too small to light the green LED.

### Values are a starting point, not fixed

The instructor stresses that these numbers come from **assumptions** (10 mA LED, β = 200, $I_{Z,min}$ = 0.5 mA). A wide range works in practice:

- $R_{B1}$: 1 kΩ or even **10 kΩ** also works, with less current drawn from the battery, *if* the Zener still conducts at that current.
- $R_{B2}$: 30 kΩ, 50 kΩ or even 100 kΩ can work, as long as the green LED stays dark and Q2 still saturates.

Follow the rules to get the first values, then adjust by testing.

### Component values

| Part | Value |
|---|---|
| Supply | 12 V battery (tested 8–12 V) |
| Q1, Q2 | BC547B (NPN, β = 200–450) |
| Zener | BZX85C10 (10 V, 9.4–10.6 V) |
| $R_{B1}$ (Zener → Q1 base) | 600 Ω calculated, 677 Ω used (10 kΩ also tested) |
| $R_{B2}$ (Q1 collector → Q2 base) | 46 kΩ |
| LED resistors | 2 × 1 kΩ |
| LEDs | Green on Q1's collector, red on Q2's collector |

---

## 5. Test Results (from the video)

With $R_{B1}$ = 677 Ω, the supply was swept:

| Supply | LED |
|---|---|
| 12 V | Green |
| 11 V | Green |
| 10.9 → 10.6 V | Green, red starts to glow faintly near 10.6 V |
| ≈ 10.5 V | **Switch point**: green off, red on |
| 10 V and below | Red |

The real switch point (≈ 10.5 V) is a bit above 10 V because the battery must also supply $V_{BE}$ and the drop on $R_{B1}$. The Zener's own tolerance (9.4–10.6 V) also shifts it. To make it switch at exactly 10 V, pick a slightly lower Zener or calibrate the resistor.

With $R_{B1}$ changed to **10 kΩ**, the circuit still switched between 10 V (red) and 11 V (green), while drawing less current from the battery. The 677 Ω version simply drives Q1 harder.

---

## 6. Other Uses: Change the Sensor, Change the Function

Only the **Zener + $R_{B1}$** block decides which LED lights. Replace it with another sensor and the same inverting pair becomes a different instrument:

- **LDR (light sensor):** green when it's bright, red when it's dark. For example, connect the LDR and a resistor as a voltage divider feeding Q1's base.
- **Thermistor (temperature sensor):** red when it's hot, green when it's not.

The threshold voltage (10 V) can also be changed (to 11 V, 12 V…) by choosing a different Zener.

---

## My Circuit Wizard Simulation

All files are in [`media/`](./media/). The GIF plays on this page (1.5× speed). Click the MP4 link for the full-quality recording.

### Battery voltage sweep, $R_{B1}$ = 10 kΩ vs 677 Ω

![Battery voltage sweep](media/ep10-sim-battery-voltage-sweep.gif)

*The battery B1 is changed by hand: 10 V → 11 V → 10 V, then down to 0 V and back up step by step. R1 is then changed from 10 kΩ to 677 Ω and the sweep is repeated. Watch the ammeter in the Zener branch: it reads 0 A whenever red is lit and jumps to µA as soon as green takes over.* · [MP4](media/ep10-sim-battery-voltage-sweep.mp4)

📁 Circuit file: [ep10-battery-monitor.cwz](./ep10-battery-monitor.cwz)

- **Setup:** B1 variable, D3 = 1N4740 (10 V Zener, used instead of the BZX85C10), R1 = $R_{B1}$ (10 kΩ, then 677 Ω), Q1 and Q2 = BC547B, R4 = $R_{B2}$ = 46 kΩ, R2 and R3 = 1 kΩ, D1 green, D2 red.
- **Meters:** voltage across the Zener, ammeter in series with the Zener ($I_{B1}$), $V_{CE}$ of Q1 and Q2, voltage across the red LED.

**With $R_{B1}$ = 10 kΩ:**

| B1 | Zener | $I_{B1}$ | $V_{CE}$ Q1 | $V_{CE}$ Q2 | Red LED D2 | Lit |
|---|---|---|---|---|---|---|
| 11 V | 9.78 V | 52.01 µA | 101.44 mV | 10.47 V | 0.52 V | **Green** |
| 10 V | 9.56 V | 0 A | 9.06 V | 46.55 mV | 2.12 V | **Red** |
| 8 V | 8.00 V | 0 A | 7.11 V | 45.77 mV | 1.83 V | **Red** |
| 3 V | 3.00 V | 0 A | 2.27 V | 50.93 mV | 1.10 V | None |
| 2 V | 2.00 V | 0 A | 1.32 V | 61.78 mV | 0.94 V | None |
| 1 V | 1.00 V | 0 A | 0.49 V | 370.68 mV | 0.63 V | None |

**With $R_{B1}$ = 677 Ω:**

| B1 | Zener | $I_{B1}$ | $V_{CE}$ Q1 | $V_{CE}$ Q2 | Red LED D2 | Lit |
|---|---|---|---|---|---|---|
| 5 V | 5.00 V | 0 A | 4.20 V | 46.26 mV | 1.39 V | **Red** (dim) |
| 10 V | 9.56 V | 0 A | 9.06 V | 46.55 mV | 2.12 V | **Red** |
| 11 V | 9.84 V | 638.47 µA | 24.20 mV | 10.48 V | 0.52 V | **Green** |

**Check: Q1's base current at 11 V (KVL around the Zener branch):**

$$R_{B1} = 677\ \Omega:\quad V_{BE} = 11 - 9.84 - (638.47\ \mu\text{A})(677\ \Omega) = 11 - 9.84 - 0.432 \approx 0.73\ \text{V} \ ✔$$

$$R_{B1} = 10\ \text{k}\Omega:\quad V_{BE} = 11 - 9.78 - (52.01\ \mu\text{A})(10\ \text{k}\Omega) = 11 - 9.78 - 0.52 \approx 0.70\ \text{V} \ ✔$$

The design target was 0.5 mA. With 677 Ω I got 0.64 mA, close and slightly above (the battery is at 11 V and the simulated Zener drops 9.84 V, not 10 V). With 10 kΩ the base current is only 52 µA, about the bare minimum $I_C/\beta = 50\ \mu\text{A}$. Q1 still saturates (101 mV), but 677 Ω drives it harder (24 mV), exactly the trade-off described in the video.

**Check: red LED current at 10 V:**

$$I_{red} = \frac{10 - 2.12 - 0.047}{1\ \text{k}\Omega} \approx 7.8\ \text{mA}$$

**Check: Q2's base current at 10 V (Q1 off):** Q1's collector sits at 9.06 V, so

$$I_{B2} \approx \frac{9.06 - 0.7}{46\ \text{k}\Omega} \approx 182\ \mu\text{A}$$

That is close to the 200 µA design value, enough to saturate Q2 ($V_{CE} \approx 47\ \text{mV}$). It also flows through the green LED, which only gets $10 - 9.06 - 0.18 \approx 0.76\ \text{V}$, far below its turn-on voltage, so green stays dark ✔.

**Expected vs. simulated:**

- Green at 11 V and red at 10 V, with both resistor values, just like the video ✔.
- At 10 V the simulated Zener sits at 9.56 V with **0 A**, meaning it is below breakdown. That is the "decision" happening.
- I only tested whole volts, so I can't see the exact switch point yet. The video found ≈ 10.5 V.
- **New finding:** below ≈ 4–5 V **neither** LED lights. Q2 is still on, but the battery can't cover the red LED's ≈ 2 V plus the resistor drop. At 5 V the red LED is dim (≈ 3.6 mA). In a real 12 V system, a battery that low is already dead, so it doesn't matter, but it's good to know.

---

## What I Learned

- A saturated transistor pulls its collector to ≈ 0 V. That makes its collector a 12 V / 0 V signal that can drive a second transistor.
- The **inverting transistor pair**: Q1 on → Q2 off, Q1 off → Q2 on. Only one LED lights at a time.
- A large base resistor (tens of kΩ) can pass enough current to switch a transistor but **not** enough to light an LED in the same path.
- A **Zener in the base path** turns a switch-controlled circuit into a **voltage-level detector**.
- Design flow: load current → $I_B = I_C/\beta_{min}$ → multiply ×2–10 for saturation → check the Zener's minimum current → solve KVL for each resistor.
- When a datasheet doesn't give $I_{Z,min}$, use the lowest test current (or measure it).
- Calculated values are a starting point: test and adjust, for example a higher $R_{B1}$ to save battery current.
- In my simulation, $R_{B1}$ = 10 kΩ draws ≈ 12× less base current than 677 Ω (52 µA vs 638 µA) and still works, but Q1 is less deeply saturated (101 mV vs 24 mV).
- The indicator itself needs a minimum supply: below ≈ 4–5 V no LED lights at all.

### Next to try

- Sweep B1 in 0.1 V steps between 10 V and 11 V to find my exact switch point and compare it with the video's ≈ 10.5 V.
- Replace the Zener + $R_{B1}$ with an LDR divider to make a light/dark indicator.
