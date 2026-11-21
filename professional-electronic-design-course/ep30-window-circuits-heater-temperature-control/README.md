# Ep30 – Window (Range) Circuits and a Heater Temperature Controller

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 30- دوائر النطاق وتصميم دوائر التحكم بسخان ودرجات الحرارة (Window circuits and designing heater and temperature control circuits)](https://www.youtube.com/watch?v=GV33C2uMbDk&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=168) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 21 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

The instructor calls this block a **"range trigger"** (قادح النطاق): a switch that turns **ON at one voltage** and **OFF at a different, lower voltage**, with a gap (the "range" or window) between them. Its usual name is the **Schmitt trigger**, and the gap is called **hysteresis**. He held back the usual name at first so the idea would be learned before the label.

1. Why a simple **one-threshold** transistor switch flickers when its input is noisy or slow.
2. The trick: give the input transistor's **emitter a voltage that changes** with the circuit's state → two thresholds.
3. The **two-transistor Schmitt trigger** with a shared emitter resistor, and how that resistor sets the window width.
4. **Design task:** a cheap heater controller (no Arduino, no microcontroller, no IC) that turns a 220 V water heater **OFF at ≈ 60 °C** and back **ON at ≈ 40 °C**, using an NTC thermistor.
5. Calculating the resistors, a power check, and a **hardware test** with a real thermistor.

Prerequisite: a good grasp of the transistor as a switch (Level 1 and 2).

---

## 1. The Problem: One Threshold → Flicker

Start with a simple two-transistor circuit:

- **Q2** drives an LED. Its base gets current from the supply through a resistor, so by default Q2 is ON and the LED is lit.
- **Q1** sits on Q2's base. When an external signal turns Q1 on, it pulls Q2's base to ≈ 0 V, so Q2 turns off and the LED goes out.
- Q1's emitter is **grounded**, so Q1 turns on at about $V_{BE} \approx 0.7\ \text{V}$ (in practice ≈ 0.8 – 1 V with the base resistor).

**Simulation in the video:** raising the input from 0.3 V to 0.6 V does nothing. At around 0.8 – 1 V, Q1 switches on, its collector falls to ≈ 0.15 V and the LED turns off.

**The flaw:** the ON and OFF decisions happen at the **same point**, with only about 0.1 V between "on" and "off". If the input comes from a sensor (battery level, temperature, light) and sits near that point, or has a little **noise** riding on it, the output keeps toggling ON–OFF–ON–OFF, like a flasher. That decision is unreliable, and for a relay or heater it's unacceptable.

**What we want:** for example, turn on above ≈ 4 V and only turn off again below ≈ 1 V. Two separate points with a range between them.

---

## 2. The Idea: Move the Emitter

Q1 needs $V_{BE} \approx 0.7\ \text{V}$ **above its emitter**:

$$
V_{in,\,th} = V_E + V_{BE}
$$

- Emitter at 0 V → it needs 0.7 V.
- Emitter at 1 V → it needs 1.7 V.

So if the emitter **isn't tied to ground** but sits on a voltage that **changes with the circuit's state**, Q1 will switch at **two different input voltages**. That is the whole trick.

---

## 3. Block: Two-Transistor Schmitt Trigger

**Connection:** Q1's and Q2's emitters are joined and go to ground through **one shared emitter resistor $R_E$**. Q2's base is fed from Q1's collector node.

**State A – input low (Q1 OFF):**
- Q1's collector node is pulled up by its resistor, so current flows into **Q2's base**, and Q2 is ON.
- Q2's base current **and** collector current both leave through its emitter, into $R_E$.
- This gives a voltage across $R_E$, e.g. ≈ 1 – 2 V. That voltage is also **Q1's emitter voltage**.
- To turn Q1 on, the input now has to reach $V_E + 0.7$: the **upper threshold**.

**State B – input high (Q1 ON):**
- Q1 pulls Q2's base low, so Q2 turns OFF.
- Q2's current through $R_E$ disappears. Only Q1's (smaller) current is left, so $V_E$ drops much lower.
- To turn Q1 off again, the input has to fall **below the new, lower** $V_E + 0.7$: the **lower threshold**.

The two currents are deliberately different, so $V_E$ is different in each state. That difference is the window.

### Simulation in the video (6 V supply)

| Step | Observation |
|---|---|
| Q2's collector with **no resistor** | ≈ 7 V across $R_E$ and **76 mA** through Q2, unrealistic |
| Added **1 kΩ** on Q2's collector | ≈ **19 mA**, ≈ 1.9 V (≈ 2 V) across $R_E$, realistic |
| Input rising | Q1 only switches at ≈ **2.7 – 3 V** (2 V on $R_E$ + 0.7 V) |
| Input falling | Q1 stays on down to 2.5 V and 2 V, and only switches back at ≈ **1.5 V** |
| Window | ≈ **1.5 V → 3 V** |
| Noise test | Wiggling the input around 1.5 V or around 3 V **doesn't change the output**: a clean, stable decision |

### $R_E$ sets the window width

The **ratio of the shared emitter resistor to the other resistors** decides how wide the window is:

| $R_E$ | Turn-on / turn-off points (input) | Window |
|---|---|---|
| 10 Ω | both ≈ 1 – 1.5 V | very narrow (almost a plain switch) |
| 100 Ω | ≈ 3 V / ≈ 1.5 V | ≈ 1.5 V |
| 300 Ω | ≈ 5 V / ≈ 2.5 V | ≈ 2.5 V |

The instructor also showed a similar textbook circuit (two transistors, 1 kΩ resistors): a **slow, gradually rising** input waits until it reaches a set level, then produces a **clean, sharp** output with no wobble.

### The hysteresis curve

Plotted as output vs. input, a Schmitt trigger gives a loop: going up, the output only switches at the upper threshold; coming down, it only switches back at the lower threshold. The width between the two is $\Delta V$ (the hysteresis), and it grows with $R_E$ relative to the collector resistors.

> **How to recognise it:** two transistors whose **emitters share one resistor to ground**, with the first transistor's collector driving the second transistor's base = **Schmitt trigger (two thresholds, noise-proof switching)**.

---

## 4. Design Task: Cheap Heater Controller

**Brief:** a company wants the **cheapest possible** control board for an electric home water heater: **no Arduino, no microcontroller, no IC**. Requirements:

- Turn the heater **OFF** above ≈ **60 °C**.
- Turn it back **ON** when the water cools to ≈ **40 °C**.
- Exact temperatures don't matter much; the important part is a **window of about 20 °C**.

Why a window? If the water drops from 60 °C to 58 °C, it's still hot, so it would be wasteful (and hard on the relay) to switch the heater on for every 1 – 2 °C. With hysteresis, the heater stays off until the water is properly cool.

### The circuit

- **Sensor:** an **NTC thermistor** (resistance falls as temperature rises) in a **voltage divider** with a fixed resistor $R$. Thermistor on top (to +9 V), $R$ on the bottom; the midpoint goes to Q1's base.
- **Schmitt trigger:** two **2N2222** NPN transistors, 1 kΩ resistors (Q1's collector pull-up, which also feeds Q2's base, and Q2's collector load), shared emitter resistor $R_E$.
- **Output:** a **PNP** transistor driven from Q2's collector switches the **LED (with 220 Ω)** and a **relay**. The relay's contact switches the **220 V AC heater** (a lamp stands in for it in the simulation).
- **Supply:** 9 V.

**Logic:** cold → thermistor resistance high → divider voltage low → Q1 OFF → Q2 ON → PNP ON → relay ON → **heater ON**. Hot → divider voltage high → Q1 ON → Q2 OFF → PNP OFF → **heater OFF**.

**Simulation note:** in Circuit Wizard the relay model drew an unrealistically high current, so a resistor was added in series with the relay just to keep it in a sensible range.

### Simulation in the video

| Temperature | Heater |
|---|---|
| ≈ 15 °C (start) | ON |
| Rising to ≈ 60 °C, slightly above | turns **OFF** |
| Falls to 50 °C | stays OFF (water is still warm) |
| Falls to ≈ 40 °C | turns **ON** again |
| Rises to 60 °C again | OFF … and the cycle repeats |

---

## 5. Measuring the Thermistor

Before choosing $R$, you need the thermistor's resistance at the two temperatures you care about.

**Test setup:** the NTC and the multimeter's temperature probe were taped side by side onto a small **aluminium** piece with **Kapton tape** (a heat-resistant tape that doesn't shrink, melt or peel). Then they were heated with hot air.

| Temperature | NTC resistance |
|---|---|
| ≈ 33 °C | ≈ 7 kΩ |
| **40 °C** | **≈ 6 kΩ** |
| **60 °C** | **≈ 2.3 kΩ** |

---

## 6. Calculating the Resistors

### Step 1 – Current through $R_E$ (state: Q2 ON, heater ON)

Assume Q2 is ON and, as a first guess, take $R_E$ as tiny (10 Ω), so the emitter node is ≈ 0 V.

- **Q2's base current** through the 1 kΩ pull-up on Q1's collector (Q1 is off, so all of it goes into Q2's base). The base sits at ≈ 0.7 V:

$$
I_B \approx \frac{9 - 0.7}{1\ \text{k}\Omega} = \frac{8.3\ \text{V}}{1\ \text{k}\Omega} \approx 8.3\ \text{mA}
$$

- **Q2's collector current** through its 1 kΩ (saturated, collector ≈ 0 V):

$$
I_C \approx \frac{9 - 0}{1\ \text{k}\Omega} = 9\ \text{mA}
$$

- Both leave through the emitter:

$$
I_E = I_B + I_C \approx 8.3 + 9 = 17.3\ \text{mA}
$$

### Step 2 – Choose $R_E$ for a useful window

- With $R_E = 10\ \Omega$: $V_E = 17.3\ \text{mA} \times 10\ \Omega \approx 0.17\ \text{V}$. Too small to make a real window.
- With $R_E = 100\ \Omega$: $V_E = 17.3\ \text{mA} \times 100\ \Omega \approx 1.7\ \text{V}$. Good.

100 Ω is still much smaller than the 1 kΩ resistors, so the 17.3 mA estimate stays roughly valid.

### Step 3 – Thresholds

- **Q2 ON (heater ON):** $V_E \approx 1.7\ \text{V}$, so Q1 needs

$$
V_{in} \approx 1.7 + 0.7 \approx 2.4 - 2.5\ \text{V}
$$

to switch the heater **off**.
- **Q1 ON (heater OFF):** Q2 is off, so $V_E$ falls close to 0 and the input has to drop well below 2.5 V before Q2 can turn back on.

**2.5 V is the pivot point.** A little above it turns the heater off; to switch it back on, the input must fall clearly below it. The instructor sketched the loop as a window about 1.7 V wide around 2.5 V (roughly 3.3 V up, 1.65 V down). These are rough, hand-calculated values.

### Step 4 – Divider resistor $R$

The heater should switch **OFF at 60 °C**, so the divider must give ≈ 2.5 V when the NTC is **2.3 kΩ**:

$$
V_{out} = V_{in}\,\frac{R}{R + R_{NTC}}
\quad\Rightarrow\quad
2.5 = 9\,\frac{R}{R + 2.3\ \text{k}\Omega}
\quad\Rightarrow\quad
R \approx 885\ \Omega
$$

Nearest standard value: **$R = 1\ \text{k}\Omega$** (it gives ≈ 2.7 V at 60 °C, a little above 2.5 V).

*My check (not from the video):* at **40 °C** (NTC ≈ 6 kΩ) the divider gives $9 \times \frac{1}{1 + 6} \approx 1.3\ \text{V}$, which is below the lower threshold, so the heater turns back on around there. At 60 °C it gives $9 \times \frac{1}{1 + 2.3} \approx 2.7\ \text{V}$, above the upper threshold.

**Practical tip:** use a **trimmer (variable resistor)** instead of the fixed 1 kΩ and calibrate it: heat the sensor to the temperature you want, watch a thermometer, and adjust until the LED/relay switches exactly there. The hand calculation only gets you close; with real parts the heater might stop at 70 or 80 °C instead of 60 °C.

### Step 5 – Power check on the 100 Ω

Small resistors (100 Ω, 220 Ω) can carry more current than they can handle, so check them. Round 17.3 mA up to 20 mA:

$$
P = I^2 R = (20\ \text{mA})^2 \times 100\ \Omega = 0.04\ \text{W} = 40\ \text{mW}
$$

The usual ¼ W (250 mW) resistor is plenty, so it won't overheat. The real currents depend on the transistors' β, so they may differ a little.

### Final values

| Part | Value |
|---|---|
| Supply | 9 V DC |
| Q1, Q2 | 2N2222 (NPN) |
| Output transistor | PNP |
| Q1 collector pull-up (feeds Q2's base) | 1 kΩ |
| Q2 collector resistor | 1 kΩ |
| Shared emitter resistor $R_E$ | 100 Ω |
| Divider resistor $R$ (bottom) | 1 kΩ (or a trimmer for calibration) |
| Sensor | NTC thermistor (≈ 6 kΩ at 40 °C, ≈ 2.3 kΩ at 60 °C) |
| LED resistor | 220 Ω |
| Load | Relay → 220 V AC heater |

---

## 7. Hardware Test

The instructor built the circuit and brought a **soldering iron close to (not touching)** the thermistor:

- The heater (relay) switched **OFF** as the sensor heated up.
- After the iron was removed, the temperature fell but the heater **did not come straight back on**. It waited until the sensor had cooled a lot further, then switched **ON** again. That's the window working in hardware.

**What he couldn't confirm:** whether it switched exactly at 60 °C and 40 °C. All the problems were **mechanical/thermal, not electrical**: with a heat spreader the sensor and the probe didn't touch the surface evenly, and even a thermal camera showed an uneven heat distribution, so he couldn't be sure what temperature the thermistor itself was at. **Fix:** mount the sensor firmly on the surface with **thermal paste**, and calibrate with a trimmer.

---

## Key Values

| Item | Value |
|---|---|
| Single-threshold switch | flips at ≈ 0.8 – 1 V, only ≈ 0.1 V between on and off |
| Demo Schmitt trigger (6 V, $R_E$ = 100 Ω, 1 kΩ collector) | ≈ 19 mA, ≈ 2 V on $R_E$, window ≈ 1.5 – 3 V |
| $R_E$ = 10 Ω / 300 Ω | narrow (≈ 1 – 1.5 V) / wide (≈ 2.5 – 5 V) |
| Heater targets | OFF ≈ 60 °C, ON ≈ 40 °C (≈ 20 °C window) |
| NTC | ≈ 7 kΩ @ 33 °C, ≈ 6 kΩ @ 40 °C, ≈ 2.3 kΩ @ 60 °C |
| Emitter current (Q2 ON) | 8.3 + 9 ≈ 17.3 mA |
| $V_E$ with $R_E$ = 100 Ω | ≈ 1.7 V |
| Upper threshold (pivot) | ≈ 2.5 V |
| Divider $R$ | 885 Ω → 1 kΩ |
| Power in 100 Ω | ≈ 40 mW (¼ W resistor OK) |

---

## My Circuit Wizard Simulation

*Not done yet.* Plan: build the 6 V demo Schmitt trigger first and sweep the input up and down to find both thresholds, then build the 9 V heater controller with a thermistor and check the 60 °C / 40 °C switching points.

---

## What I Learned

- A plain transistor switch has **one threshold**, so a noisy or slow sensor signal makes it chatter. Bad for relays and heaters.
- A transistor turns on at **$V_E + 0.7\ \text{V}$**, not always 0.7 V. Making $V_E$ depend on the circuit's state gives **two thresholds**.
- **Schmitt trigger block:** two transistors sharing one emitter resistor. When Q2 is on, its base + collector current lifts the emitter, so the input must go higher to switch; once switched, the emitter drops, so the input must fall lower to switch back.
- **$R_E$ (vs. the collector resistors) sets the window width:** 10 Ω ≈ no window, 100 Ω ≈ 1.5 V, 300 Ω ≈ 2.5 V.
- Design flow for a sensor + Schmitt trigger: **measure the sensor** at the target points → **estimate the currents** assuming a state → **pick $R_E$** for the window → **solve the divider** for the switching point → **check resistor power** → **calibrate with a trimmer**.
- In hardware tests, **thermal contact** (paste, firm mounting) matters as much as the circuit.

### Next to try

- Simulate the single-threshold circuit with a small noise signal on the input and watch the LED chatter, then repeat with the Schmitt trigger.
- Sweep the input up and down with $R_E$ = 10, 100 and 300 Ω and record both thresholds for each.
- Build the full heater controller in Circuit Wizard (thermistor, PNP, LED, relay, 220 V lamp) and check that the heater stays off between 60 °C and 40 °C.
- Replace the 1 kΩ divider resistor with a trimmer and find the setting that switches off at exactly 60 °C.
- Measure (or simulate) the actual currents and compare them with the 17.3 mA hand estimate.
