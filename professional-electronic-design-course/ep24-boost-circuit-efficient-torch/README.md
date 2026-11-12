# Ep24 – Designing a Boost Circuit for an Efficient Torch with a Small Battery

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 24- تصميم دائرة رفع جهد لدائرة كشاف يدوي فعال مع بطارية صغيرة (Designing a boost circuit for an efficient torch with a small battery)](https://www.youtube.com/watch?v=I3QhjudxgAU) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 12 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

So far the oscillator ([ep23](../ep23-random-selection-circuit-oscillator/)) has only flashed LEDs. This episode puts it to real work: it drives a **DC-to-DC boost converter** that raises a small battery voltage to a much higher DC voltage.

The episode has five parts:

1. **How an inductor reacts** when its current starts or stops, and why that makes it act like a **short-lived battery**.
2. The **basic boost circuit**: battery, inductor, diode, capacitor and a switch.
3. **Simulations**: switching by hand, then with a transistor driven by a function generator.
4. A **design challenge**: power **6 white LEDs in series** (≈ 18 V) from a single **3.7 V lithium cell**, by combining the oscillator with the boost circuit. Then the same circuit built on real hardware.
5. A **two-transistor boost circuit found online**, and how commercial products do the same job.

Before this episode you need to understand the **two-transistor oscillator** (astable multivibrator) from [ep23](../ep23-random-selection-circuit-oscillator/) and the **transistor / MOSFET as a switch** ([ep14](../ep14-secret-code-circuit-no-programming/)).

---

## 1. The Oscillator as a Square-Wave Source (Recap)

In the classic two-transistor oscillator, two LEDs take turns: one on, one off, at a rate set by the R and C values.

- The LEDs aren't required. Remove one and leave **only its resistor** (220 Ω, or bigger: 500 Ω, 1 kΩ, even 10 kΩ all work).
- The **collector** of that transistor then gives a **square wave**, which can be sent to another circuit.

That square wave is what will switch the boost converter on and off.

---

## 2. What a Boost (DC-to-DC) Converter Does

A **DC-to-DC converter** turns one DC voltage into a different DC voltage. A **boost** (step-up) converter makes it **higher**:

| Input | Needed by the circuit | Example |
|---|---|---|
| 3 V | 10 V | a circuit that can't run on 3 V |
| 5 V | ≈ 100 V | a higher-voltage circuit |
| 5 V | ≈ 500 V | special high-voltage uses |

The idea is the same in all of them. Only the details change (extra protection, regulation, etc.) depending on the application.

---

## 3. The Inductor: the Secret Behind It

An inductor is just a coil of wire, but it **opposes any change in its current**.

### When current starts

1. The current is 0 A, then suddenly tries to jump to, say, 1 A or 10 A.
2. The inductor builds a **magnetic field** around itself.
3. That field cuts through the turns of the coil and **induces a voltage** that pushes **against** the incoming current.
4. For that short moment the inductor behaves like a **battery** (with + and − terminals) opposing the supply.
5. After a while the current wins, the field stops changing, and the inductor is just a **wire** again.

### When current stops

If a large current (say 5 A) is suddenly reduced or cut:

1. The inductor again opposes the change, this time trying to **keep the current flowing**.
2. It induces a voltage with the **opposite polarity**, so now it is a short-lived battery that **pushes in the same direction** as the supply.

$$
v_L = L\,\frac{di}{dt}
$$

*(my note, standard formula)*: the faster the current changes ($di/dt$) and the bigger the inductance $L$, the bigger the voltage.

### Two batteries in series

When the current is cut, the inductor's voltage is in **series** with the supply battery, so the two add up:

$$
V_{out} = V_{battery} + V_{L} \qquad \text{e.g. } 12\ \text{V} + 10\ \text{V} = 22\ \text{V}
$$

The battery is only 12 V, but the output reaches 22 V. That's a **voltage booster**.

If the current is switched **on and off fast enough**, the inductor never settles into being "just a wire". It keeps producing these voltage pulses, and the output keeps getting boosted.

---

## 4. The Basic Boost Circuit

| Part | Role |
|---|---|
| Battery (e.g. 5 V) | low-voltage input |
| Inductor (≈ 1 – 10 mH) | stores energy and produces the voltage kick |
| Diode | lets the high-voltage pulse **out** to the output, and stops it **flowing back** |
| Capacitor | **stores** the boosted voltage (not always needed, but needed in many applications) |
| Switch → later a transistor | connects the inductor/diode node to ground and releases it, again and again |

The switch sits from the **node between the inductor and the diode** to **ground**.

**Switch closed:** the inductor is effectively shorted to ground. A **large current** flows through it, and none goes to the load.

**Switch open:** the current now has to go through the diode to the load, which allows a **much smaller current** (e.g. from 10 A down to 1 A). That sudden drop makes the inductor produce a voltage that **adds** to the battery, and the high voltage charges the capacitor / feeds the load.

The diode is what stops this high voltage from flowing back toward the lower-voltage side.

**My note (standard formula, not from the video):** for an ideal boost converter running continuously with duty cycle $D$ (the fraction of time the switch is ON):

$$
V_{out} = \frac{V_{in}}{1 - D}
$$

With **no load**, there's nothing to take charge from the capacitor, so the output keeps climbing, which is what the simulations below show.

---

## 5. Simulations from the Video

### a) Switching by hand

| Part | Value |
|---|---|
| Battery | 9 V |
| Inductor | 1 mH |
| Switch | manual |
| Diode + capacitor | yes, output measured across the capacitor |

- At power-up the capacitor already charged to about **15 V** (from the inductor's response when the circuit first turns on).
- Each press and release of the switch pushed the output higher: **≈ 22 V**, then **≈ 25 V**, from only a 9 V battery.

This proves that an **inductor + diode + switch** really can raise a DC voltage.

### b) Transistor driven by a function generator

The switch was replaced by a **transistor**, driven by Circuit Wizard's **function generator** (5 V square wave).

| Setting | Value |
|---|---|
| Battery | 3.7 V |
| Drive | 5 V square wave, high frequency (the video says "200 000 Hz … 20 kHz"; the two numbers don't match, so I'll check the exact value in my own simulation) |
| Load | none |

- With a high frequency the output rises **smoothly and gradually**, like a ramp, not in visible steps.
- From 3.7 V it climbed to **≈ 5 V**, then **≈ 10 V**, and kept going. With no load it can keep rising (even to 50 or 100 V).
- Six or so LEDs on this output would light from a 3.7 V cell. With the oscillator driving it, the output can reach around **15 V**.

At **100 Hz**, with the time control at **100 ms**, it still rose to **≈ 9.5 – 10 V** from 3.7 V.

The parts you need: **inductor + diode + transistor** (MOSFET, NPN or PNP) **+ an oscillator**.

**Simulation tips from the video:**

- If nothing seems to happen, **change the time control** (try 1 s, 100 ms, 1 ms) until you can see the change.
- In the oscillator, swap the µF capacitors for **100 nF** to get a **higher frequency**. At low frequency the boost may not work well.

---

## 6. Design Challenge: a Hand Torch on One Lithium Cell

### The task

- Supply: one **3.7 V lithium** cell.
- Load: **6 white LEDs in series**, after the boost capacitor.
- Each white LED needs about **3 – 4 V**, so 6 in series need about:

$$
6 \times 3\ \text{V} = 18\ \text{V}
$$

How do you get ≈ 18 V from 3.7 V? Combine the **oscillator** with the **boost circuit**.

### The solution

It's simply the two blocks joined together:

- The **two-transistor oscillator** runs from the **same 3.7 V battery** (220 Ω collector resistors in the simulation; up to 10 kΩ also works in practice).
- Its output (a collector) drives the **boost transistor** (2N2222) on and off.
- Inductor → diode → capacitor → 6 white LEDs in series.

### Simulation (time control ≈ 1 ms)

- The output rose from 3.7 V → 4 V → 4.5 V → 5 V → 6.5 V …
- The LEDs started to glow, **very faintly**, at about **6 V**, then the output crept to 7, 8, 9 V **slowly**.

**Important point about boost converters:** if the **load draws a lot of current**, the capacitor can't charge up, so the boost converter is **limited** in how high it can go. Here the LEDs drew only ≈ **3 mA**, and that was already enough to slow the rise in the simulation. On real hardware it is much faster and goes higher.

### MOSFET version

To use a **MOSFET** instead of the BJT:

- Connect the oscillator output to the **gate**, through a resistor (1 kΩ, or lower).
- Always add a **10 kΩ – 100 kΩ resistor from gate to ground**. It drains the charge left on the gate so the MOSFET really turns off.

In the simulation the MOSFET version shot up much faster: **60 V, 240 V, 400 V**. That doesn't mean it's better, and 400 V is **not realistic**. It's a simulation effect with no real losses.

---

## 7. The Real Hardware Test

### How much voltage do 6 white LEDs need?

The instructor connected the 6 LEDs straight to a bench supply and turned it up:

| Supply | LEDs |
|---|---|
| 3.7 V … 14 V | off |
| 15 V | just starting |
| 15.7 V | glowing a little |
| ≈ 16 V + | properly on, brighter as the voltage rises |

So the target is **at least ≈ 15 – 16 V** from 3.7 V.

### The board

| Part | Value |
|---|---|
| Oscillator capacitors | 100 nF (for fast switching) |
| Oscillator resistors | marked "10" on the board; in the simulation use **220 Ω** if 10 doesn't oscillate |
| Switch | **IRFZ44** MOSFET (to show you don't always need an NPN/PNP) |
| Gate pull-down | 10 kΩ |
| Inductor | **2.2 mH** (looks like a resistor, rated for small currents, good for this kind of job) |
| Diode | ordinary diode |
| Output capacitor | small, about 0.3 µF (not large, but enough) |
| Load | 6 white LEDs in series |

### Results

- At **3.7 V** the LEDs lit, so the output was about **15 – 16 V**.
- A fully charged lithium cell is **4.2 V**, so the torch is at its brightest then.
- Lowering the supply: still on at **3 V** (output ≈ 15 V, about **5×** the input), still on at 2.9, 2.8, 2.7 V, and only starting to dim at **≈ 2.6 – 2.5 V**.
- To show it's not the bench supply doing the work: a tiny **3.7 V, 150 mAh** lithium cell (about the size of a finger joint), only **half charged**, lit all 6 LEDs fully.
- **12 white LEDs in series** (needs over 20 – 30 V) also lit well.
- On 6 LEDs the cell lasted **more than 6 hours**.

### A commercial torch

A small store-bought torch has **9 white LEDs** and runs on **3 × 1.5 V = 4.5 V**. If all 9 were in series they'd need about $9 \times 3 = 27$ V to $9 \times 4 = 36$ V. (With 10 LEDs you might instead use two strings of 5 in parallel.) Either way, 4.5 V can only light them through a **boost circuit** inside. The instructor couldn't open it to show the circuit.

---

## 8. A Boost Circuit Found Online

A similar circuit that does the same job, and maybe better:

- **3.7 V** battery, **inductor**, and a string of LEDs as the load.
- **No separate diode is needed**: the LEDs are diodes themselves. A capacitor can be added across the output if you want.
- What's needed is something to **switch the inductor node on and off**, and that's done by **two transistors** forming a different kind of oscillator from the one in ep23:
  - **Q1** switches the inductor node to ground.
  - Q1's emitter goes to ground through a **10 Ω resistor**, so when Q1 conducts a voltage appears across it. (If the emitter went straight to ground, no voltage would form.)
  - That voltage, with the help of another resistor, charges a **capacitor**, which then turns **Q2** on.
  - **Q2** pulls Q1's base to ground, so **Q1 turns off**. Then the cycle restarts.
  - Result: Q1 turns on, and after a short delay it's turned off → a stream of **pulses** to the LEDs.
- On a scope, the output node looks **spiky**. With a capacitor across it, it becomes **smooth (DC)**.

How to read it: Q1's base is fed through a **capacitor**, not directly or through a resistor, which is a strong hint that it's an oscillating / delay circuit. Oscillators come in many different shapes.

### Results (from the video)

- At 3.7 V, the 6 series LEDs lit, so the output was **over 18 V** (assuming ≈ 3 V per LED).
- **Brightness stayed almost constant** down to about **3 V**, which is the strength of this circuit: its resistors are chosen to hold the light level steady. It started to fade around **2.8 V**.
- At **4.2 V** (full cell) the light was excellent.
- With the 3.7 V, 150 mAh cell it ran for **over 6 hours**. Adding a second string of 6 (12 in series) still lit them all at about the same brightness.

---

## 9. How Real Products Do It

This is a practical, marketable circuit: a very small battery driving many LEDs. To sell it you'd add a **small charging circuit** (or supply a charger).

Big companies use **the same structure** (inductor, switch, diode, capacitor), but they:

- use a **stronger, tightly controlled oscillator** instead of a simple two-transistor one;
- choose **better inductors**: the winding type and **core** decide how hot they get and how much current they can handle;
- often use a **dedicated IC** that does all of this, with **feedback** that **regulates the output voltage and current**.

---

## Summary of Values from the Video

| Circuit | Item | Value |
|---|---|---|
| Oscillator output | Collector resistor in place of an LED | 220 Ω (500 Ω – 10 kΩ also work) |
| Hand-switched sim | Battery / inductor | 9 V / 1 mH |
| Hand-switched sim | Output | ≈ 15 V at power-up → ≈ 22 – 25 V after several presses |
| Function generator sim | Battery / drive | 3.7 V / 5 V square wave, high frequency |
| Function generator sim | Output (no load) | ≈ 5 V → ≈ 10 V and rising |
| 100 Hz sim | Output | ≈ 9.5 – 10 V (time control 100 ms) |
| Torch challenge | Supply / load | 3.7 V Li cell / 6 white LEDs in series |
| Torch challenge | Target voltage | ≈ 6 × 3 V = 18 V |
| Torch sim (BJT, 2N2222) | Output | slow rise to ≈ 9 V; LEDs faint at ≈ 6 V; load ≈ 3 mA |
| Torch sim (MOSFET) | Gate resistor / pull-down | 1 kΩ / 10 – 100 kΩ |
| Torch sim (MOSFET) | Output | 60 → 240 → 400 V (unrealistic) |
| Hardware | 6 white LEDs turn on at | ≈ 15.7 – 16 V |
| Hardware | Oscillator caps | 100 nF |
| Hardware | MOSFET / gate pull-down | IRFZ44 / 10 kΩ |
| Hardware | Inductor | 2.2 mH |
| Hardware | Output capacitor | ≈ 0.3 µF |
| Hardware | Output at 3.7 V | ≈ 15 – 16 V |
| Hardware | Still lit down to | ≈ 2.6 V (dims at 2.5 V) |
| Hardware | Battery / run time | 3.7 V, 150 mAh / > 6 h on 6 LEDs |
| Hardware | 12 LEDs in series | lit |
| Store torch | LEDs / supply | 9 white / 3 × 1.5 V = 4.5 V |
| Online circuit | Emitter resistor | 10 Ω |
| Online circuit | Steady brightness down to | ≈ 3 V (fades ≈ 2.8 V) |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add the hand-switched boost, the oscillator + boost torch (BJT and MOSFET), and the two-transistor online circuit here.

---

## What I Learned

- An inductor **opposes changes in current**. For a short moment after the current starts or stops, it acts like a **battery**; when the current is cut, that battery is in **series** with the supply, so the voltages **add**.
- A **boost converter** is just **inductor + switch + diode (+ capacitor)**: the switch shorts the inductor to ground to build up current, then releases it so the voltage kick goes out through the diode.
- The **diode** stops the boosted voltage flowing back; the **capacitor** stores it.
- Replace the switch with a transistor and drive it with an **oscillator** (ep23), and a 3.7 V cell can light **6 or even 12** white LEDs in series.
- **Higher frequency** works better (100 nF instead of µF in the oscillator).
- **Load current limits the boost**: with no load the output keeps rising; a heavy load stops the capacitor from charging.
- With a **MOSFET**, always add a **gate-to-ground resistor** (10 – 100 kΩ). Simulations can show **unrealistic** voltages (400 V) because they ignore real losses.
- Commercial boost converters use the **same structure** with better inductors and an IC that has **feedback** to regulate voltage and current.

### Next to try

- Build the 9 V / 1 mH hand-switched boost and record the output after each press.
- Build the 3.7 V oscillator + boost torch with 6 white LEDs, first with a 2N2222 and then an IRFZ44 + 10 kΩ pull-down. Compare how fast and how high the output rises.
- Find the **actual switching frequency** used in the video (200 kHz vs 20 kHz) and see how the output changes with frequency.
- Compare the ideal $V_{out} = V_{in}/(1-D)$ with what I get, and see how far the output drops as I add more load current.
- Simulate the **two-transistor online circuit** and check whether the brightness really stays steady as the supply falls from 4.2 V to 3 V.
