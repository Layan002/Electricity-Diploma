# Ep9 – Designing a Motor Control Switch Monitoring Circuit with LEDs

**Course:** Professional Electronic Design Course – Level 1 (Simple Electronic Design)
**Source:** Waleed Issa Academy – [Designing a motor control switch monitoring circuit with LEDs](https://www.youtube.com/watch?v=gldZtiZqnuU&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=147) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

**The task:** a switch (SW1) connects a 9–12 V battery to a DC motor. Add two LEDs so you can tell the switch state at a glance:

| SW1 | Motor | LED |
|---|---|---|
| Open | Stopped | **Red** only |
| Closed | Running | **Green** only |

You may only use basic parts (resistors, diodes, Zener diodes, transistors, capacitors). No ICs.

It sounds trivial, but if you try it with just two LEDs and resistors, either both LEDs light at the same time or neither does. The episode solves it with one key idea about LEDs and then uses that idea in **three different designs**.

**Prerequisite:** how an LED is forward-biased and its typical forward voltage.

---

## 1. The Key Idea: an LED Holds Its Own Voltage

### The experiment

A red LED with a 330 Ω resistor is connected to a variable supply, and a voltmeter is placed across the LED only. The supply is swept from 5 V to 15 V:

| Supply | Red LED voltage | Green LED voltage |
|---|---|---|
| 5 V | ≈ 2.16 V | ≈ 2.19 V |
| 10 V | ≈ 2.3 V | ≈ 2.24 V |
| 15 V | ≈ 2.4 V | ≈ 2.25 V |

The supply triples, but the LED voltage barely moves. All of the extra voltage drops across the resistor.

**Conclusion:** a lit LED behaves like a small **voltage regulator**. It holds about $V_F \approx 2\text{–}2.4\ \text{V}$ across itself, whatever the supply.

### Why this matters

Anything connected **in parallel** with a lit LED gets that same ≈ 2 V and no more.

- If the parallel branch needs **less than 2 V** to conduct (for example another LED alone), it turns on too.
- If the branch needs **more than 2 V**, it stays **off**.

So we can **force one branch off** by making it need more voltage than the other LED allows:

$$V_{\text{branch}} > V_{F,\text{green}} \approx 2\ \text{V} \quad\Rightarrow\quad \text{branch stays off}$$

---

## 2. Design 1: Three Diodes in Series with the Red LED

### The circuit

- **Red branch:** $+12\ \text{V} \rightarrow D_1 \rightarrow D_5 \rightarrow D_4\ (\text{3 silicon diodes}) \rightarrow \text{red LED} \rightarrow \text{node } X$
- **Green branch:** $+12\ \text{V} \rightarrow SW_1 \rightarrow \text{node } Y \rightarrow \text{green LED} \rightarrow \text{node } X$
- **Motor:** between node $Y$ and ground.
- **Shared resistor:** $R = 1\ \text{k}\Omega$ from node $X$ to ground.

Both LED branches are connected **between the same two points** ($+12\ \text{V}$ and $X$), which puts them in parallel when the switch is closed.

### How it works

**Switch open:** node $Y$ is pulled to 0 V through the motor, so the green LED is reverse-biased and off. The red branch needs only:

$$3 \times 0.7 + 2 \approx 4.1\ \text{V} \ll 12\ \text{V}$$

so the red LED lights.

**Switch closed:** node $Y$ jumps to 12 V. The green LED turns on and holds ≈ 2 V between $+12\ \text{V}$ and $X$. The red branch now sees only ≈ 2 V, but it needs ≈ 4.1 V, so the red LED goes off. The motor runs.

Two diodes would probably be enough ($2 \times 0.7 + 2 = 3.4\ \text{V}$). Three are used to leave a safe margin.

---

## 3. Design 2: a Zener Diode Instead of the Diodes

Same layout, but the three diodes are replaced with one **Zener diode** (5.1 V), connected in **reverse** (cathode toward +12 V).

**Switch open:** 12 V is more than the $5.1 + 2 \approx 7.1\ \text{V}$ the red branch needs. The Zener breaks down and the red LED lights.

**Switch closed:** the green LED clamps the branch to ≈ 2 V, which is far below 7.1 V. The Zener cannot break down, so the red LED goes off.

It's the same idea as Design 1: the Zener simply "raises" the voltage the red branch needs.

**Important:** the two branches must be **in parallel** (joined at the same node $X$). If each LED had its own resistor to ground, both branches would get the full 12 V and both LEDs would light.

---

## 4. Design 3: a Transistor Shorts the Red LED

This one uses a different idea: an NPN transistor connected **across the red LED** (collector to anode, emitter to cathode).

- The base is fed from the switched node $Y$ through a base resistor (1 kΩ to 4.7 kΩ). It only needs a small current to saturate the transistor.
- **Switch open:** no base current, so the transistor is off and current flows through the red LED. Red lights.
- **Switch closed:** base current flows, the transistor saturates, and its collector–emitter short-circuits the red LED. Current bypasses the LED, so red goes off.

### The problem: a shared resistor

In the first version both LEDs still shared **one** 1 kΩ resistor to ground. When the switch was closed, the green LED stayed off. Here's why:

- The transistor now passes about $10.8\ \text{mA}$ through the shared resistor.
- That drops about $10.8\ \text{mA} \times 1\ \text{k}\Omega \approx 10.8\ \text{V}$ across it.
- The green LED is left with only about $12 - 10.8 \approx 1.2\ \text{V}$ (my simulation shows ≈ 0.98 V, see [simulation 4](#4-design-3-transistor-shared-resistor-wrong-connection)). That is below its ≈ 2 V turn-on voltage, so it stays dark.

### The fix: separate resistors

Split the shared resistor into **two resistors, one per LED branch**. Each LED is now independent:

- Green branch current: ≈ 9.37 mA, so the drop across its own 1 kΩ is ≈ 9.4 V.
- That leaves $12 - 9.4 \approx 2.6\ \text{V}$ for the green LED, which is enough to light it.

> **Note:** this is the opposite of Designs 1 and 2. Those depend on a shared node (parallel branches). Design 3 uses a transistor to switch the red LED, so the branches must be **separate**.

---

## 5. Hardware Test and Comparison

In the hardware build, a small lamp is used as the load instead of the motor. All three boards work: press the switch and green lights with the lamp, release it and red comes back.

| Design | Extra parts | Heat | Notes |
|---|---|---|---|
| 1. Three diodes | 3 × silicon diode | Cool | Simplest and cheapest |
| 2. Zener | 1 × 5.1 V Zener | Zener gets slightly warm | Fewest parts |
| 3. Transistor short | NPN + base resistor + extra resistor | Resistor gets warm (current flows through it all the time) | Most parts |

### Component values

| Part | Value |
|---|---|
| Supply | 12 V |
| Diodes (Design 1) | 3 × silicon (≈ 0.6–0.7 V each) |
| Zener (Design 2) | 1N751, 5.1 V |
| LED resistor(s) | 1 kΩ (hardware ≈ 330 Ω each in Design 3) |
| Transistor base resistor (Design 3) | 1 kΩ – 4.7 kΩ |
| LEDs | Red (switch open), green (switch closed) |

---

## My Circuit Wizard Simulations

All files are in [`media/`](./media/). The GIFs play on this page. Click the MP4 link for the full-quality recording.

### 1. Design 1: three diodes

![Three diodes switching](media/ep9-sim-three-diodes-switching.gif)

*SW1 is closed at the start and the green LED (D3) is lit with the motor running. When SW1 opens, the green LED goes off and the red LED (D2) lights. Watch each diode's voltage drop from ≈ 0.59 V to ≈ 0.46 V when the green LED takes over.* · [MP4](media/ep9-sim-three-diodes-switching.mp4)

- **Setup:** 12 V, D1/D5/D4 silicon diodes, D2 red, D3 green, VR2 = 1 kΩ shared resistor, MT1 motor.

| Measurement | SW1 open | SW1 closed |
|---|---|---|
| Each diode (D1, D5, D4) | 586.62 mV | ≈ 456 mV |
| Red LED D2 | **2.16 V (on)** | 0.94 V (off) |
| Green LED D3 | −8.08 V (reverse, off) | **2.31 V (on)** |
| Shared resistor VR2 | 8.08 V → 8.08 mA | 9.54 V → 9.54 mA |
| Battery current | 8.08 mA | 1.23–1.46 A (motor) |

**Check (switch open):**

$$I = \frac{12 - 3(0.587) - 2.16}{1\ \text{k}\Omega} = \frac{8.08\ \text{V}}{1\ \text{k}\Omega} = 8.08\ \text{mA} \ ✔$$

**Check (switch closed):** voltage across the red branch is $12 - 9.54 = 2.46\ \text{V}$. That is close to the green LED's 2.31 V (the rest is lost in wiring and the ammeter). The red branch needs about 3.9 V, so it stays off ✔.

### 2. Design 2: Zener diode

![Zener switching](media/ep9-sim-zener-switching.gif)

*SW1 is toggled several times. Each time it closes, the motor spins up and green takes over; each time it opens, red comes back. Watch the Zener's voltage drop below 5.1 V when green is on, which proves it has stopped conducting.* · [MP4](media/ep9-sim-zener-switching.mp4)

- **Setup:** 12 V, D3 = 1N751 (5.1 V Zener), D1 red, D2 green, VR1 = 1 kΩ shared resistor, MT1 motor.

| Measurement | SW1 open | SW1 closed |
|---|---|---|
| Zener D3 | 5.11 V (in breakdown) | 4.68 V (below 5.1 V, off) |
| Red LED D1 | **1.68 V (on)** | −2.23 V (off) |
| Green LED D2 | −5.21 V (reverse, off) | **≈ 2.4–2.45 V (on)** |
| Shared resistor VR1 | 5.21 V → 5.21 mA | ≈ 9.4–9.5 V → ≈ 9.4 mA |
| Motor | 0 V | 11.82 V |
| Battery current | 5.21 mA | ≈ 0.85–1.46 A (motor) |

**Check (switch open):**

$$5.11 + 1.68 + 5.21 = 12.0\ \text{V} \ ✔ \qquad I = \frac{5.21\ \text{V}}{1\ \text{k}\Omega} = 5.21\ \text{mA}$$

**Check (switch closed):** the red branch has only ≈ 2.45 V across it but needs ≈ 7 V, so the Zener and red LED are both off ✔. The −2.23 V on the red LED is not a real "reverse" condition. When two parts in series are both off, the simulator splits the small voltage between them arbitrarily.

### 3. Design 3: transistor, separate resistors (working)

![Transistor design with separate resistors](media/ep9-sim-transistor-separate-resistors.gif)

*The corrected transistor version: each LED has its own 1 kΩ resistor (VR1 for red, VR3 for green). SW1 is toggled. When it closes, Q1 switches on, the red LED (D1) goes dark and the green LED (D2) lights with the motor. When it opens, red comes back. Watch the voltage across D1 fall to ≈ 1 V when Q1 is on.* · [MP4](media/ep9-sim-transistor-separate-resistors.mp4)

- **Setup:** 12 V, Q1 NPN with VR2 = 10 kΩ base resistor, D1 red, D2 green, VR1 = 1 kΩ (red branch), VR3 = 1 kΩ (green branch), MT1 motor.

| Measurement | SW1 open | SW1 closed |
|---|---|---|
| Red LED D1 (= Q1's $V_{CE}$) | **2.41 V (on)** | 1.03 V (off, bypassed by Q1) |
| Green LED D2 | 0 V (off) | **2.48 V (on)** |
| Q1 $V_{BE}$ | −9.58 V (off) | 703.77 mV (on) |
| Base resistor VR2 | 0 V | 321.61 mV → ≈ 32 µA |
| Red-branch resistor VR1 | 9.58 V → 9.58 mA | 10.97 V → 10.97 mA (through Q1) |
| Green-branch resistor VR3 | 0 V | 9.51 V → 9.51 mA |
| Motor | 0 V | 11.99 V |
| Battery current | 9.58 mA | ≈ 1.31 A (motor) |

**Check (switch closed):**

$$V_{D2} + V_{VR3} = 2.48 + 9.51 = 11.99\ \text{V} \ ✔ \qquad I_{green} = \frac{9.51\ \text{V}}{1\ \text{k}\Omega} = 9.51\ \text{mA}$$

The green current matches the video's 9.37 mA closely. Q1 holds only 1.03 V across the red LED, which is well below its ≈ 2 V turn-on, so red stays off ✔. Q1 isn't fully saturated here (a saturated transistor would show ≈ 0.2 V), because the 10 kΩ base resistor gives only ≈ 32 µA of base current. It still works, since 1 V is enough to keep the LED off. A smaller base resistor (1–4.7 kΩ, as in the video) would push $V_{CE}$ lower.

### 4. Design 3: transistor, shared resistor (wrong connection)

![Transistor design with one shared resistor](media/ep9-sim-transistor-shared-resistor-fault.gif)

*The first, wrong version: both LEDs and the transistor return to ground through **one** shared 1 kΩ resistor (VR4). With SW2 open the red LED (D3) lights normally. But when SW2 closes and the motor runs, the green LED (D4) **never lights**, because it gets less than 1 V.* · [MP4](media/ep9-sim-transistor-shared-resistor-fault.mp4)

- **Setup:** same as above, but Q2, D3 red, D4 green, VR5 = 10 kΩ base resistor, and a single VR4 = 1 kΩ for everything, MT2 motor.

| Measurement | SW2 open | SW2 closed |
|---|---|---|
| Red LED D3 | **2.41 V (on)** | 985.46 mV (off) |
| Green LED D4 | −9.58 V (reverse, off) | 983.98 mV (**off: the fault**) |
| Q2 $V_{BE}$ | −9.58 V (off) | 699.74 mV (on) |
| Shared resistor VR4 | 9.58 V → 9.58 mA | 10.83 V → 10.83 mA |
| Motor | 0 V | 11.82 V |
| Battery current | 9.58 mA | ≈ 1.49 A (motor) |

**Why green stays off:** Q2's current flows through the shared resistor and drops most of the supply across it:

$$V_{VR4} = 10.83\ \text{mA} \times 1\ \text{k}\Omega = 10.83\ \text{V} \quad\Rightarrow\quad V_{left} = 12 - 10.83 \approx 1.17\ \text{V}$$

That leaves ≈ 1 V for the green LED (I measured 0.98 V), but it needs ≈ 2 V to turn on. Compare it with simulation 3: the only change is splitting VR4 into VR1 + VR3, and green jumps from 0.98 V (off) to 2.48 V (on).

**Expected vs. simulated:** all three designs behave as predicted. Only one LED is lit at a time, and the green LED current (≈ 9.4–9.5 mA) matches the value in the video (9.37 mA). The shared-resistor fault from the video also shows up exactly as explained.

---

## What I Learned

- A lit LED acts as a **≈ 2 V regulator**. Anything in parallel with it is limited to that voltage.
- To keep a parallel branch **off**, make it need **more** than that voltage: add series diodes ($n \times 0.7\ \text{V}$) or a Zener ($V_Z$).
- A transistor across a load can **short it out** to turn it off. With this approach, though, each LED needs its **own resistor** so the shorting current doesn't steal the other LED's voltage.
- The same goal can be reached with different parts. Compare designs by **part count, cost, and heat**.
- The motor current (≈ 1.2–1.5 A) goes straight through the switch. The indicator circuit only draws a few mA, so it doesn't affect the motor.

### My notes / things to watch

- **LED current:** with 1 kΩ the LEDs run at 5–10 mA, which is bright enough and well below the 20 mA rating. In the Zener design the red LED is dimmer (5.2 mA) because the Zener takes 5.1 V of the supply.
- **Supply dependence:** for Design 2, the red LED only lights if $V_{supply} > V_Z + V_F \approx 7.1\ \text{V}$. That is fine at 9–12 V, but a weak battery would turn red off. Design 1 works down to ≈ 4–5 V.
- **Green/red brightness:** in Designs 1 and 2 green is brighter (≈ 9.5 mA) than red (5–8 mA), because the red branch loses voltage across the diodes or the Zener.
