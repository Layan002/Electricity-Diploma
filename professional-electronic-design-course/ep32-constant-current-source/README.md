# Ep32 – Running on a Constant Current (Constant Current Source)

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 32- تشغيل على تيار ثابت (Constant Current Source)](https://www.youtube.com/watch?v=cenJbimRFq0&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=170) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 26 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

We already know **constant voltage** sources. This episode adds a new block: the **constant current source**, a circuit that pushes the **same current** through its load (LEDs, a motor, a heater…) whatever the supply voltage or the load does.

1. Why a plain resistor can't give a constant current.
2. The core idea: a **fixed voltage across a fixed resistor**, applied "remotely" through a transistor's base.
3. Ways to make the reference voltage: diodes, Zener, regulator, diode-connected transistor, $V_{BE}$ multiplier, op-amp.
4. **Current source vs. current sink.**
5. Regulators as current sources: **7805** and **LM317**.
6. Spotting current sources in big schematics.
7. **Design task:** a two-mode torch (5 or 10 white LEDs) with the same brightness in both modes, plus a hardware test.

Prerequisite: a good grasp of the transistor (Levels 1 and 2), and the diode-connected transistor and $V_{BE}$ multiplier from [ep29](../ep29-unusual-uses-of-the-transistor/).

---

## 1. Why a Resistor Alone Isn't Enough

Ohm's law: $I = V / R$. If either $V$ or $R$ changes, $I$ changes.

| Case | Current |
|---|---|
| 9 V across 10 Ω + 10 Ω | $9 / 20 = 0.45\ \text{A}$ |
| Supply raised to 12 V | $12 / 20 = 0.6\ \text{A}$ |
| Supply back to 9 V, load resistance changed | current changes again |

**LED example:** 9 V, a 150 Ω resistor and white LEDs taking about 2 V each:

$$
I = \frac{9 - n \cdot 2\ \text{V}}{150\ \Omega}
$$

| LEDs in series | Current | Result |
|---|---|---|
| 3 | 20 mA | correct brightness |
| 2 | ≈ 33 mA | brighter |
| 1 | ≈ 46 mA | much brighter (and stressed) |

Remove an LED and the current jumps. To keep the same brightness however many LEDs are connected, we need a **constant current source**.

---

## 2. The Core Idea: Fixed Voltage Across a Fixed Resistor

If $V$ and $R$ are both fixed, $I = V/R$ is fixed. The trick is how to fix the voltage across the resistor **without the load being part of that calculation**.

<!-- Add figure: media/ep32-basic-current-sink.png -->

**Basic NPN circuit:**

- The **load** (any number of LEDs) is in the **collector**.
- A resistor $R$ is in the **emitter**, down to ground.
- The **base** is held at a fixed voltage: one diode (≈ 0.6 V, matching the transistor's $V_{BE}$) in series with a fixed reference $V$.

Tracing the voltages:

$$
V_B = V + 0.6 \quad\text{and}\quad V_B = V_{BE} + V_R = 0.6 + V_R
\quad\Rightarrow\quad V_R = V
$$

The diode cancels the base–emitter drop, so the **reference voltage appears across the emitter resistor**. It was applied "remotely", through another branch.

$$
I_{load} \approx I_E = \frac{V}{R}
$$

The collector current doesn't care how many LEDs are in the load, **as long as their total voltage stays below the supply** (otherwise no current flows).

### Making the reference voltage

A second battery isn't practical. Anything that holds a steady voltage works:

- **One or more diodes:** each adds ≈ 0.6–0.7 V (more diodes → more voltage → more current).
- **Zener diode.**
- **Voltage regulator** (e.g. 7805).
- **Diode-connected transistor** (from ep29).

Whatever the circuit looks like, the theory is always the same: **fix the base voltage, subtract $V_{BE}$, divide by the resistor.**

---

## 3. Current Source vs. Current Sink

The name depends on where the regulating part sits relative to the load:

| Name | Where the regulator is | Current flow |
|---|---|---|
| **Constant current sink** (مصرف تيار) | **below** the load | current passes through the load, then *drains into* the regulator |
| **Constant current source** (مصدر تيار) | **above** the load | the regulator *supplies* current to the load, and the load goes to ground |

The names don't change how the circuit works, but knowing them helps when reading datasheets.

---

## 4. Worked Examples

### 4.1 Zener + NPN → current sink

- 5.6 V Zener on the base, 10 kΩ emitter resistor.

$$
I = \frac{5.6 - 0.6}{10\ \text{k}\Omega} = 0.5\ \text{mA}
$$

Load above the transistor → **current sink**.

### 4.2 Three diodes + PNP → current source

- 10 V supply, three diodes from the top rail → base at $10 - 3 \times 0.6 = 8.2\ \text{V}$.
- PNP: going *against* the emitter arrow, **add** $V_{EB}$ → emitter at $8.2 + 0.6 = 8.8\ \text{V}$.
- 560 Ω emitter resistor to +10 V.

$$
I = \frac{10 - 8.8}{560\ \Omega} \approx 2\ \text{mA}
$$

Transistor above the load → **current source**.

**Rule for adding voltages:** stepping across the base–emitter junction *in the arrow's direction* subtracts ≈ 0.6 V. Stepping *against* it adds ≈ 0.6 V.

### 4.3 $V_{BE}$ multiplier as the reference

The $V_{BE}$ multiplier from ep29 gives a more stable reference (less sensitive to supply and temperature changes). With $V_{ref} = 1.5\,V_{BE}$:

$$
I = \frac{1.5\,V_{BE} - V_{BE}}{56\ \Omega} = \frac{0.5 \times 0.7}{56} \approx 6.25\ \text{mA}
$$

That is very close to the 6 mA labelled on the schematic.

### 4.4 Op-amp + transistor

- A voltage divider sets $V_{in}$ on one op-amp input.
- The op-amp drives the transistor so that its other input (the resistor/emitter node) **equals** $V_{in}$.
- The voltage across the resistor is fixed at $V_{CC} - V_{in}$:

$$
I = \frac{V_{CC} - V_{in}}{R}
$$

Advantages: **no $V_{BE}$ error** (the 0.6 V drops out), and the op-amp's **feedback** corrects any drift. It's more complex but stronger. With a PNP above a grounded load, it's a **current source**.

---

## 5. Regulators as Current Sources

### 7805

The 7805 holds **5 V between OUT and GND**. Put a resistor between those two pins and connect the GND pin to the load:

$$
I = \frac{5\ \text{V}}{R}
$$

### LM317

The LM317 holds **1.25 V between OUT and ADJ**:

$$
I = \frac{1.25\ \text{V}}{R}
$$

In the datasheet circuit this gives a constant current from about **5 mA up to 1.5 A**.

⚠️ At high currents (0.5–1 A), check the resistor's power ($P = I^2 R$) and pick a larger wattage so it doesn't overheat.

---

## 6. Spotting Current Sources in Big Schematics

Datasheets often draw a current source as a **circle with an arrow and a current value**. Inside, it's one of the circuits above.

| Example | What's inside |
|---|---|
| Large IC schematic: complementary output, current-limit resistor, crossover diodes (already covered), plus **arrow symbols labelled with currents** | Each arrow symbol is a constant current source. The **200 µA** one works out as $(1.24\ \text{V} - 0.6\ \text{V}) / R$. |
| Transistor with a fixed base voltage, 15 V supply | $I = (15 - V_E)/R = 50\ \mu\text{A}$. It **charges a capacitor**. |
| Divider → base, 499 Ω emitter resistor | ≈ **2 mA** source. |
| Two diodes biasing **three** transistors with different emitter resistors | One reference, several sources: **2 mA** in one branch, **10 mA** in another. |

### Why charge a capacitor with constant current?

With a resistor, a capacitor charges along a curve that flattens out. With a **constant current**, the voltage rises in a **straight line** (a linear ramp):

$$
V_C = \frac{I \cdot t}{C}
$$

This is how ramp and sawtooth waveforms are generated.

You don't need to analyse every resistor. Recognise the block, write "50 µA source", and move on.

---

## 7. Design Task: Two-Mode Torch

**Torch spec:**

- 10 white LEDs in **two groups of 5**. Within a group the LEDs are in **parallel**, and the two groups are in **series**.
- A **switch** shorts the second group. Switch closed → 5 LEDs on. Switch open → all 10 on.
- Each LED: **≈ 3 V, 5 mA** → each group draws $5 \times 5 = 25\ \text{mA}$.
- Supply: **9 V**.
- **Goal:** the same brightness whether 5 or 10 LEDs are on.

### 7.1 Resistor-only attempt (fails)

$$
R = \frac{9 - 3 - 3}{25\ \text{mA}} = 120\ \Omega
$$

In the video's simulation: **39 mA** with both groups on and **53 mA** with one group. (The simulator's LED voltages differ from real ones, so the values aren't exactly 25 mA.) The point is that **the current changes**, so the brightness changes.

### 7.2 Constant current solution

Based on the PNP current source (§4.2):

<!-- Add figure: media/ep32-torch-current-source.png -->

| Part | Value | Why |
|---|---|---|
| Supply | 9 V | |
| Diode | ≈ 0.7 V | cancels the transistor's $V_{EB}$ |
| Zener | 2.7 V | reference (use any value you have, e.g. 3.3 V or 5 V, and recalculate) |
| Zener bias resistor | 1 kΩ | from experience, gives the Zener a good, steady current |
| Transistor | PNP | source above the LEDs |
| Emitter resistor | 100 Ω | sets 25 mA (see below) |

**Calculation:**

- Base: $9 - 0.6 - 2.7 \approx 5.7\ \text{V}$ (measured 5.7 V in the simulation).
- Emitter (against the arrow, add ≈ 0.7 V): $\approx 6.4\ \text{V}$.

$$
R_E = \frac{9 - 6.4}{25\ \text{mA}} = 104\ \Omega \;\rightarrow\; 100\ \Omega \text{ (nearest standard value)}
$$

Each LED gets $25 / 5 = 5\ \text{mA}$.

> Note to self: with 2.6 V across $R_E$ and ≈ 6 V across two LED groups, only ≈ 0.4 V is left across the transistor in 10-LED mode. That's very little headroom, so it's worth checking in simulation.

There isn't one "right" design. Any values that give a fixed 25 mA in both modes are correct.

### 7.3 Results

| Test | Expected | Result |
|---|---|---|
| Simulation, both groups | 25 mA | **25 mA** (5 mA per LED) |
| Simulation, one group | 25 mA | **25 mA** |
| Hardware, first group only | 25 mA | **≈ 26 mA** |
| Hardware, switch pressed | 25 mA | **≈ 25 mA** |
| Hardware, brightness | same | no visible change between modes |
| Voltage across the Zener | 2.7 V | **2.65 V** |

### 7.4 Practical tip: if the current isn't constant

Then the **reference voltage isn't constant**. Measure across the Zener:

- If it's well below its rating (e.g. 2.0–2.1 V instead of 2.7 V), the Zener isn't getting the right current. **Change the bias resistor** (up or down) until you read the rated voltage.
- The better the Zener is biased, the steadier the output current.

These simple circuits work best at **low currents**. High currents need different designs. Cheap commercial 9 V torches are built exactly this way.

---

## What I Learned

- **Constant current = constant voltage across a constant resistor.** Every version (diodes, Zener, $V_{BE}$ multiplier, op-amp, 7805, LM317) is Ohm's law used cleverly.
- Putting the reference on the **base** copies it (minus $V_{BE}$) onto the emitter resistor, so the load is out of the equation.
- **Source** sits above the load and **sink** below.
- An arrow with a current value in a datasheet is a current source block, and constant-current charging gives a linear ramp on a capacitor.
- When a current source misbehaves, check the reference voltage first.

This circuit is reused in [ep33](../ep33-current-mirrors-circuits-without-resistors/), where the 25 mA torch source feeds a current mirror.

---

## Files

- `media/` – Circuit Wizard simulations and figures (to be added)
