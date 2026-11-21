# Ep25 – Designing a Sequential Oscillator Circuit for Decorative Lights

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 25- تصميم دائرة المذبذب المتسلسل لأضواء الزينة (Designing a sequential oscillator circuit for decorative lights)](https://www.youtube.com/watch?v=8bDQ0pfS_I8&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=163) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 21 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

The two-transistor flasher from [ep23](../ep23-random-selection-circuit-oscillator/) makes **two** LEDs take turns. This episode copies its basic block several times in a ring to get a **sequential oscillator** (an LED "chaser", also called a ring oscillator): the light moves from one LED to the next, then wraps around to the first. These are the running lights you see in shop windows and decorations, and here they are made with **only transistors, resistors and capacitors**, no programming.

The episode has four parts:

1. **From flasher to sequential oscillator**: repeating the same RC block in a ring.
2. **Making the ON move instead of the OFF**: moving the LED so it sits in parallel with the transistor.
3. **Controlling each step's time** with its capacitor, and using the sequence to switch real loads in order.
4. A **design challenge**: driving **5 red, 5 green and 5 yellow LEDs** in sequence in a practical way, with a driver transistor per colour and full resistor calculations.

Before this episode you need to understand the **two-transistor flasher** and the **flip-flop capacitor** ([ep22](../ep22-flip-flop-capacitor-oscillator-status-indicator/), [ep23](../ep23-random-selection-circuit-oscillator/)), and the **transistor as a switch** with base-resistor calculations (Level 1).

---

## 1. From Flasher to Sequential Oscillator

### The flasher, redrawn

Take the ep23 flasher and draw both transistors **facing the same way**. Nothing changes electrically, only the drawing. Now each stage is clearly the same block:

| Part of one block | Connection |
|---|---|
| NPN transistor | emitter to ground |
| LED + 220 Ω | from +V to the collector |
| Base resistor (10 kΩ) | from +V to the base |
| Coupling capacitor | from **this** collector to the **next** transistor's base |

In the two-stage flasher, transistor 1's collector feeds transistor 2's base, and transistor 2's collector feeds back to transistor 1's base. When a transistor turns on, its collector drops and the capacitor's "flip" (ep22) pushes the next base below 0 V, turning that transistor off until the capacitor recharges through its base resistor.

### Repeat the block

Instead of sending the last capacitor straight back to the first transistor, **add more blocks in between**:

- Collector 1 → capacitor → base 2
- Collector 2 → capacitor → base 3
- …
- Collector of the **last** stage → capacitor → base 1 (closes the ring)

The video uses **3 stages**, but 10 or even 100 work the same way, as long as the supply can feed them all.

### What the simulation shows

- At power-up all LEDs light while the capacitors charge (the simulation is slow, so this takes a moment).
- Then the pattern starts, but what moves around the ring is the **OFF**: one LED is dark and the others are lit, and the dark spot walks 1 → 2 → 3 → 1 …

That's because in each stage, at any moment, **one transistor is held off** by its capacitor and the rest are on. An LED in series with the collector lights when its transistor is **on**, so most LEDs are lit.

---

## 2. Making the ON Move Instead of the OFF

For decorative lights (and for switching loads one at a time), you want **one ON** that moves, not one OFF.

### Option A: an inverter transistor per stage

Put another transistor after each stage to invert it. It works, but it adds **3 more transistors** to a circuit that only has 3.

### Option B (used in the video): put the LED in parallel with the transistor

Keep the 220 Ω resistor from +V to the collector, but move the LED so it goes from the **collector to ground**, in parallel with the transistor:

| Transistor | What happens | LED |
|---|---|---|
| **ON** (saturated) | the transistor acts like a short across the LED and takes the current | **OFF** |
| **OFF** | current flows from +V through 220 Ω into the LED | **ON** |

Now the stage that is held **off** is the one that lights, so the **ON** walks around the ring.

> My note: the "short" is really the transistor's saturation voltage, $V_{CE(sat)} \approx 0.2$ V, which is well below an LED's forward voltage (≈ 2 V), so the LED really goes out.

---

## 3. Timing and Applications

### Each capacitor sets the time of the next stage

The capacitor (with the base resistor of the next stage) controls how long the **next** transistor stays off, so it sets how long that stage's step lasts. Each stage can therefore have its own time, e.g. step 1 = 5 s, step 2 = 1 s, step 3 = 10 s, just by choosing the capacitors.

> My note (standard astable approximation, not from the video): each off-time is roughly $t \approx 0.69\,R_B C$. With $R_B = 10$ kΩ and $C = 220$ µF that gives $t \approx 1.5$ s per step.

### Hardware test (from the video)

| Part | Value |
|---|---|
| Supply | 9 V |
| Stages | 3 |
| Collector resistors | 220 Ω |
| Base resistors | 10 kΩ |
| Coupling capacitors | 220 µF each (slow enough to see by eye) |
| LEDs | in parallel with each transistor |

- The LEDs lit one after another (1 → 2 → 3 → 1), so the **ON** was moving.
- Changing **only the last capacitor** from 220 µF to **33 µF** made one LED's step much shorter: it just blinked briefly compared with the other two. That capacitor controls the stage after it.

### Where to use it

Any job that needs things to happen **one after another**:

- At home: a heater, then a TV, then a bell or a door, each in its turn.
- In a factory: a conveyor runs, then a motor, then a valve, each for its own time.

The circuit can be repeated as many times as you like; the only limit is that the supply has to be able to feed all the stages.

---

## 4. Design Challenge: 5 Red, 5 Green, 5 Yellow LEDs

### The task

Instead of one LED per stage:

- Stage 1 drives **5 red** LEDs.
- Stage 2 drives **5 green** LEDs.
- Stage 3 drives **5 yellow** LEDs.

They should light in sequence, and the design should be **practical**.

### Why not just add LEDs next to the existing one?

The obvious idea is to put 5 (or 10) LEDs in parallel where the single LED was. But every LED draws current through the **same 220 Ω resistor**:

$$
10 \times 10\ \text{mA} = 100\ \text{mA}
$$

100 mA through one small resistor makes it **very hot**. A high-power resistor could take it, but with 50 LEDs it gets worse, and the oscillator stage itself starts to carry the load current. Not practical.

### The practical approach: one driver transistor per colour

1. **Remove the LEDs** from the oscillator. The collector node of each stage becomes an output (OUT1, OUT2, OUT3), still with its 220 Ω resistor.
2. Send each output through a **base resistor** to a separate **driver transistor** (2N2222 / BC547).
3. The driver feeds its group of LEDs. The oscillator now only supplies a small base current, and the LED current comes straight from the supply.

In the video's circuit the driver is connected with its **collector to +9 V** and its **emitter to the LED group** (the 5 LEDs in parallel to ground), so the LEDs sit at about 2 V and the rest of the voltage drops across the driver.

| Oscillator stage | Its output | Driver | LEDs |
|---|---|---|---|
| ON | ≈ 0 V | off | off |
| OFF | pulled up toward 9 V through 220 Ω | on | **on** |

Because the lit group is the one whose stage is **off**, the ON still moves: red → green → yellow → red …

**Circuit Wizard tip:** instead of drawing long wires, use **terminals (connectors)** and give two of them the same name (e.g. OUT1). The program treats them as connected.

### Calculating the base resistor

Design values:

| Quantity | Value |
|---|---|
| Supply | 9 V |
| LEDs per colour | 5 in parallel |
| Current per LED | 10 mA (could be 15 – 20 mA) |
| LED voltage at 10 mA | ≈ 2 V (≈ 2 – 2.1 V from experience) |
| Transistor | 2N2222 or BC547, $\beta \approx 200$ (average from the datasheet) |

**Collector (load) current:**

$$
I_C = 5 \times 10\ \text{mA} = 50\ \text{mA}
$$

This is the limit: much more than this and the LEDs are damaged.

**Base current:**

$$
I_B = \frac{I_C}{\beta} = \frac{50\ \text{mA}}{200} = 250\ \mu\text{A}
$$

**Voltage across the base resistor.** The left end sees 9 V when the stage is off. The right end is the base, which is one $V_{BE}$ above the LEDs:

$$
V_B = V_{BE} + V_{LED} = 0.7 + 2 = 2.7\ \text{V}
$$

**Base resistor:**

$$
R_B = \frac{9 - 2.7}{250\ \mu\text{A}} = \frac{6.3\ \text{V}}{250\ \mu\text{A}} \approx 25\ \text{k}\Omega
$$

Anything close to 25 kΩ is fine (not 1 kΩ, not 100 kΩ). The video uses **22 kΩ**, the nearest value available in the lab, which gives about **10 mA per LED**, and the same for the other two colours.

> My check: with 22 kΩ, $I_B = 6.3 / 22\,000 \approx 286\ \mu\text{A}$, so $I_C \approx 200 \times 286\ \mu\text{A} \approx 57$ mA, about 11.5 mA per LED. Still safe. The simulation in the video showed roughly **44 – 54 mA** per group and **≈ 2 V** across the LEDs.

### The drawback: the driver works in the linear region

The LEDs take ≈ 2 V and the supply is 9 V, so about **7 V** is left across the driver transistor, not the ≈ 0.2 V of a saturated switch. The transistor is in its **linear (active) mode** and will **warm up a bit**.

> My note: $P \approx V_{CE} \times I_C \approx 7\ \text{V} \times 50\ \text{mA} = 0.35$ W. That's fine for a 2N2222 with 5 LEDs.

For many more LEDs, use a **bigger transistor with a heatsink**. For 5 LEDs at 50 mA the 2N2222 is fine. The point is to **know** this limitation when designing.

> My note: the LED current here is set by $\beta \times I_B$, and $\beta$ varies from part to part, so the brightness (and safety margin) depends on the transistor. A common alternative is to saturate the driver and give each LED its own series resistor.

### What about a PNP driver?

Some people would prefer a **PNP** driver with its emitter straight on 9 V and the LEDs below it, which is the familiar high-side switch. The video tried it: the PNP drivers **invert** the oscillator outputs, so the **OFF** moves again (one colour dark, the others lit). Some people like that pattern, but to get red → green → yellow with a PNP you'd need extra inverting transistors. The NPN arrangement gives the wanted pattern with **only one driver per colour**.

### The full circuit

- 3-stage sequential oscillator, 9 V, 220 µF in each stage.
- Each output → 22 kΩ → NPN driver.
- A long row of LEDs wired so that every third LED is the same colour: red, green, yellow, red, green, yellow … (all reds on driver 1, all greens on driver 2, all yellows on driver 3).

Result: green, yellow, red, green, yellow, red … running along the row, just like commercial decorative light strings.

---

## 5. Why This Matters

- This is a **real commercial product** that has been sold for years, built from this same circuit repeated many times with many LEDs and colours.
- It needs no microcontroller and no programming, just transistors, resistors and capacitors.
- If something fails, it's usually one transistor or resistor, so it's **easy to repair**.
- Cheap versions sometimes use weak transistors that run hot and occasionally burn out, but the design is still simple, and once you understand the calculations you can see why each value was chosen and change it.

### Thinking in blocks

The big lesson: start from one **block** you understand (the flasher stage), then **copy and connect** it to build something bigger. In large schematics, try to see blocks ("this part is an oscillator, that part is a driver") instead of single transistors. This leads straight into Level 3, which is all about circuit blocks ([ep26](../ep26-introduction-to-level-3-work-plan/)).

---

## Summary of Values from the Video

| Circuit | Item | Value |
|---|---|---|
| Sequential oscillator | Supply | 9 V |
| Sequential oscillator | Stages | 3 (can be extended to 10, 100 …) |
| Sequential oscillator | Collector resistor | 220 Ω |
| Sequential oscillator | Base resistor | 10 kΩ |
| Sequential oscillator | Coupling capacitors | 220 µF each |
| Sequential oscillator | Short step test | last capacitor 220 µF → 33 µF |
| Sequential oscillator | LED position for moving ON | collector to ground (parallel with the transistor) |
| Design challenge | LEDs per colour | 5 (red / green / yellow) |
| Design challenge | Current per LED / per group | 10 mA / 50 mA |
| Design challenge | Driver transistor | 2N2222 or BC547, $\beta \approx 200$ |
| Design challenge | Base current | 250 µA |
| Design challenge | Base voltage | 0.7 + 2 = 2.7 V |
| Design challenge | Calculated / used base resistor | ≈ 25 kΩ / 22 kΩ |
| Design challenge | Simulated group current | ≈ 44 – 54 mA, LEDs at ≈ 2 V |
| Design challenge | Voltage across driver | ≈ 7 V (linear mode, warms) |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add the 3-stage chaser (LED in series vs. in parallel with the transistor), the 33 µF short-step test, and the red/green/yellow driver circuit here.

---

## What I Learned

- A **sequential oscillator** is the ep23 flasher block repeated in a ring: each collector feeds the **next** base through a capacitor, and the last one feeds back to the first.
- With the LED in series with the collector, the **OFF** moves. Putting the LED **in parallel with the transistor** (fed through the 220 Ω) makes the **ON** move, with no extra transistors.
- Each **capacitor sets the time of the stage after it**, so every step can have its own duration.
- Don't hang many LEDs on the oscillator's own collector resistor: take a signal from each stage and use a **driver transistor** per group.
- Base resistor design: $I_B = I_C/\beta$, then $R_B = (V_{in} - V_{BE} - V_{LED}) / I_B$ → ≈ 25 kΩ, 22 kΩ used.
- An emitter-side LED load leaves ≈ 7 V across the driver, so it runs in the **linear region** and warms up; fine for 50 mA, use a bigger transistor and heatsink for more.
- A **PNP** driver inverts the pattern (OFF moves again).
- Thinking in **blocks** makes big circuits easier to read and design.

### Next to try

- Build the 3-stage chaser in Circuit Wizard, first with the LED in series, then in parallel with the transistor, and record which one moves the ON.
- Change one capacitor to 33 µF and measure each step's time; compare with $t \approx 0.69\,R_B C$.
- Build the red/green/yellow driver circuit with 22 kΩ base resistors and measure each group's current and the driver's $V_{CE}$.
- Try a PNP driver and confirm that the pattern inverts.
- Try a saturated-switch driver with a resistor per LED and compare the current spread when $\beta$ changes.
