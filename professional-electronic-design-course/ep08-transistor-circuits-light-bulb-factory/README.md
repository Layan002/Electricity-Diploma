# Ep8 – Various Transistor Circuits and Circuit Design for a Light Bulb Factory

**Course:** Professional Electronic Design Course – Level 1 (Simple Electronic Design)
**Source:** Waleed Issa Academy – [Various transistor circuits and circuit design for a light bulb factory](https://www.youtube.com/watch?v=qVGQhxMi4MI&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=146) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

The episode introduces two small "building block" circuits and then combines them into one design:

1. **Push-pull (complementary) transistor pair** – chooses which of two LEDs lights, based on a high or low input.
2. **Current-sensing transistor** – turns a transistor on only when the current through a small resistor exceeds a set value.
3. **Design task: a light bulb tester** – a factory makes 12 V bulbs that should draw **1 A or less**. Build the cheapest circuit that shows a **red** LED if a bulb draws more than 1 A and a **green** LED otherwise.

**Prerequisite:** how NPN and PNP transistors switch on and off.

---

## 1. Push-Pull Transistor Pair

### The problem

Two LEDs in series with resistors across the supply always light together. There is no way to light only one of them.

### The circuit

An NPN transistor (Q1) and a PNP transistor (Q2) are stacked between the supply and ground:

- Q1 (NPN): collector to $+V$, emitter to the middle node.
- Q2 (PNP): emitter to the middle node, collector to ground.
- Both **bases are tied together** and driven by one input.
- The middle node connects to the point between the two LEDs:
  - $+V \rightarrow R_1 \rightarrow D_1 \rightarrow$ middle node
  - middle node $\rightarrow D_2 \rightarrow R_2 \rightarrow$ ground

### How it works

The idea is to **steer the current** by shorting one LED:

| Input | Q1 (NPN) | Q2 (PNP) | Middle node | Result |
|---|---|---|---|---|
| High ($\approx V_{CC}$) | ON | OFF | pulled up to $\approx V_{CC} - 0.7\ \text{V}$ | D1 is shorted (same voltage on both sides), **D2 lights** |
| Low ($0\ \text{V}$) | OFF | ON | pulled down to $\approx 0.7\ \text{V}$ | D2 is shorted, **D1 lights** |

- An NPN turns on when its base is about $0.7\ \text{V}$ **above** its emitter.
- A PNP turns on when its base is about $0.7\ \text{V}$ **below** its emitter.

Because one input turns one transistor on and the other off, **exactly one LED is lit at a time**. Two NPNs would not work: they would turn on and off together.

---

## 2. Current-Sensing Transistor

### The idea

Instead of feeding the base from a battery or a signal, put a small **sense resistor** $R_s$ between base and emitter, in the path of the load current. By Ohm's law:

$$V_{BE} = I_{load} \cdot R_s$$

The transistor turns on once $V_{BE} \approx 0.6\text{–}0.7\ \text{V}$, so the **trip current** is:

$$I_{trip} \approx \frac{0.6\text{–}0.7\ \text{V}}{R_s}$$

Examples:

- $R_s = 1\ \Omega \Rightarrow I_{trip} \approx 0.7\ \text{A}$
- $R_s = 0.6\ \Omega \Rightarrow I_{trip} \approx 1\text{–}1.1\ \text{A}$

### Simulation in the video ($R_s = 0.6\ \Omega$)

| Load current | $V_{BE} = I \cdot 0.6\ \Omega$ | LED |
|---|---|---|
| 0.5 A | 0.30 V | OFF |
| 1.0 A | 0.60 V | OFF (not quite enough) |
| 1.1 A | 0.66 V | ON |
| 2.0 A | 1.2 V (clamped by the B–E junction) | ON, brighter |

**Takeaway:** a transistor with a resistor between base and emitter is a **current sensor**. It "decides" whether the current is above a chosen limit.

---

## 3. Design Task: Light Bulb Tester

### Combining the blocks

- The current sensor (Q3 + $R_s$) carries the bulb's current.
- Its LED is replaced by a **pull-up resistor $R_3 = 4.7\ \text{k}\Omega$** from $+12\ \text{V}$ to Q3's collector.
- Q3's collector is the **output**, and it drives the tied bases of the push-pull pair.

| Bulb current | Q3 | Output (bases) | Push-pull | LED |
|---|---|---|---|---|
| $> 1\ \text{A}$ | ON | pulled to ground | Q2 (PNP) ON | **Red** (D1) |
| $\le 1\ \text{A}$ | OFF | pulled high through $R_3$ | Q1 (NPN) ON | **Green** (D2) |

### Component values

| Part | Value |
|---|---|
| Supply | 12 V |
| Q1, Q3 | BC547B (NPN) |
| Q2 | 2N5401 (PNP) |
| $R_s$ (R4) | 0.6 Ω (simulation) / 3 × 1.5 Ω in parallel = 0.5 Ω (hardware) |
| $R_3$ (pull-up) | 4.7 kΩ |
| $R_1$, $R_2$ (LED resistors) | 220 Ω |
| D1 / D2 | Red / Green LED |

### Calibrating the pull-up $R_3$

$R_3$ sets how hard Q3 must conduct to pull the output low, so it acts like a sensitivity adjustment:

- **10 kΩ (too high):** at 0.9 A, Q3 is only partly on, yet that is enough to pull the output low. The red LED glows dimly when it shouldn't.
- **1 kΩ (too low):** at 1 A, the red LED does not light. At 1.1 A, red and green light together because Q3 cannot fully pull the output down.
- **4.7 kΩ (middle):** clean switching around 1 A. Q3 saturates fully above the limit and stays off below it.

In simulation at exactly 1 A both LEDs light a little. This is the transition zone between the two states.

### Hardware build

A 0.6 Ω resistor wasn't available, so three **1.5 Ω** resistors were connected in parallel:

$$R_s = \frac{1.5\ \Omega}{3} = 0.5\ \Omega \qquad V_{BE} = 1\ \text{A} \times 0.5\ \Omega = 0.5\ \text{V}$$

According to the BC547 datasheet, it can start conducting around 0.5 V, so the trip point lands near 1 A in practice.

**Power check at 1 A** (each resistor carries $1/3$ of the current):

$$P = I^2 R = (0.333\ \text{A})^2 \times 1.5\ \Omega \approx 0.167\ \text{W} = 167\ \text{mW}$$

That is below the **250 mW (¼ W)** rating, so the resistors are safe at 1 A.

### Measured results (from the video)

| Test | Current drawn | LED |
|---|---|---|
| No bulb | ≈ 0.038 A (circuit only) | Green |
| Bulb 1 | ≈ 1.6 A | **Red** ✔ |
| Bulb 2 | ≈ 0.19 A | **Green** ✔ |

The circuit correctly classified both bulbs.

---

## My Circuit Wizard Simulations

All files are in [`media/`](./media/). Each GIF plays on this page. Click the MP4 link for the full-quality recording.

### 1. Push-pull pair

![Push-pull switching](media/ep8-sim-push-pull-switching.gif)

*Toggling SW1 moves the shared base input between +9 V and 0 V, so the lit LED swaps each time. Watch the middle node: when it is pulled high, the red LED (D2) lights; when it is pulled low, the green LED (D1) lights.* · [MP4](media/ep8-sim-push-pull-switching.mp4)

📁 Circuit file: [ep8-push-pull.cwz](./ep8-push-pull.cwz)

![Push-pull still](media/ep8-push-pull-sim.png)

- **Setup:** 9 V battery (the video uses 12 V), SW1 drives the tied bases of Q1 (NPN) and Q2 (PNP), $R_1 = R_2 = 220\ \Omega$.
- **Expected vs. simulated:**

  | SW1 (input) | Transistor ON | LED lit | Matches theory? |
  |---|---|---|---|
  | High (+9 V) | Q1 (NPN) | D2 (red, bottom) | ✔ |
  | Low (0 V) | Q2 (PNP) | D1 (green, top) | ✔ |

- Only one LED is ever lit, which confirms the "current steering" idea.
- **Estimated LED current** (assuming ≈ 2 V LED drop):

$$I_{LED} \approx \frac{9 - 0.7 - 2}{220\ \Omega} \approx 29\ \text{mA}$$

### 2. Current sensor threshold

![Current sense threshold](media/ep8-sim-current-sense-threshold.gif)

*The load current (I1) is stepped up from 1 A to 1.5 A while the sense resistor stays at 0.6 Ω. Watch the LED: it stays off at 1 A, glows faintly at 1.1 A, and gets brighter as the current rises.* · [MP4](media/ep8-sim-current-sense-threshold.mp4)

📁 Circuit file: [ep8-current-sense-threshold.cwz](./ep8-current-sense-threshold.cwz)

![Current sensing still](media/ep8-current-sense-sim.png)

| $I_1$ | Expected $V_{BE} = I_1 \times 0.6\ \Omega$ | Simulated LED |
|---|---|---|
| 1.0 A | 0.60 V | OFF |
| 1.1 A | 0.66 V | ON (dim) |
| 1.2 A | 0.72 V | ON (bright) |
| 1.5 A | 0.90 V → clamped near 0.7 V | ON (bright) |

**Result:** the transistor switches on between 1.0 A and 1.1 A, exactly as predicted. Brightness keeps rising slightly above the threshold because Q1 is moving from partly on toward fully on (saturation).

### 3. Complete bulb tester (current sweep)

![Bulb tester sweep](media/ep8-sim-bulb-tester-sweep.gif)

*The load current I1 (the "bulb") is swept 0 A → 1 A → 1.1 A → 1.2 A → 0.5 A. Watch the two LEDs: green alone means OK, red alone means over 1 A, and both lit together marks the transition zone.* · [MP4](media/ep8-sim-bulb-tester-sweep.mp4)

📁 Circuit file: [ep8-bulb-tester.cwz](./ep8-bulb-tester.cwz)

![Bulb tester still](media/ep8-current-checker-sim.png)

- **Setup:** full design. $I_1$ = load, $R_4 = 0.6\ \Omega$, Q3 BC547B, $R_3 = 4.7\ \text{k}\Omega$, Q1 BC547B, Q2 2N5401, D1 red (top), D2 green (bottom), $R_1 = R_2 = 220\ \Omega$.

| $I_1$ | Expected | Simulated | OK? |
|---|---|---|---|
| 0 A | Green | Green only | ✔ |
| 0.5 A | Green | Green only | ✔ |
| 1.0 A | Green (limit) | **Red + green** | ⚠ transition |
| 1.1 A | Red | **Red + green** (green fainter) | ⚠ transition |
| 1.2 A | Red | Red only | ✔ |

**Comparison with the video:** in the video, red alone lights at 1.1 A. In my run, green is still partly on at 1.1 A and the clean switch happens at about 1.2 A. Q3 is not yet fully saturated at 1.1 A, so the output sits between high and low and both push-pull transistors conduct a little.

**How I could sharpen it:**
- Raise $R_3$ a little (e.g. 5.6 kΩ) so Q3 pulls the output low with less current. This moves the switch point down toward 1.0–1.1 A. Going as high as 10 kΩ overshoots and makes red glow below 1 A, as the video showed.
- Or raise $R_4$ slightly (e.g. 0.62–0.65 Ω) so $V_{BE}$ reaches 0.7 V sooner.

---

## What I Learned

- Complex designs are built from **small blocks**. Learn each block, then connect an output of one to the input of the next.
- A **complementary NPN/PNP pair with tied bases** lets one signal select between two loads.
- A **resistor between base and emitter** turns a transistor into a current-threshold detector: $I_{trip} \approx 0.7\ \text{V} / R_s$.
- A **pull-up resistor** converts "transistor on/off" into a clean high/low output for the next stage, and its value affects sensitivity.
- Always check **resistor power** ($P = I^2R$) when large currents flow through sense resistors.

### My notes / things to watch

- **Sense resistor power at higher currents:** the 1.6 A bulb puts about 0.53 A through each 1.5 Ω resistor:

  $$P \approx (0.533)^2 \times 1.5 \approx 0.43\ \text{W}$$

  That is above the ¼ W rating. For a quick test this is fine, but for repeated use I'd choose **½ W or 1 W** resistors.
- **Trip point tolerance:** with 0.5 Ω and $V_{BE}$ between 0.5 and 0.7 V, the real trip point is roughly **1.0–1.4 A**. The hardware test used 0.19 A and 1.6 A bulbs, which are far from the limit. A bulb at about 1.1 A would be a better test of accuracy.
- **LED current at 12 V:** $(12 - 0.7 - 2) / 220\ \Omega \approx 42\ \text{mA}$, which is above the typical 20 mA LED rating. A **470 Ω** resistor would give about 20 mA.
- $V_{BE}$ depends on temperature, so the trip point drifts slightly as the transistor warms.
