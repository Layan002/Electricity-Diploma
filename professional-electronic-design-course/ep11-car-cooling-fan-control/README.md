# Ep11 – Designing a Car-Factory Panel to Control the Cooling Fan

**Course:** Professional Electronic Design Course – Level 1 (Simple Electronic Design)
**Source:** Waleed Issa Academy – [تصميم لوحة لمصنع سيارات للتحكم بمروحة التبريد (Designing a car-factory panel to control the cooling fan)](https://www.youtube.com/watch?v=MS73p18R1EE&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=149) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

**The task:** a car factory wants a small circuit that switches the engine's **cooling fan** on when the temperature rises and off when it falls. One rule applies: in a car, every load must have its negative side **permanently connected to the battery negative (the chassis/body)**.

To get there, the episode answers three questions:

1. Why do we want a switching transistor to be **saturated**? (Answer: heat.)
2. Why can't an NPN transistor placed **above** a grounded load saturate?
3. How does the **driver transistor** circuit (a small NPN driving a PNP) solve this?

The design then adds a **thermistor** in a voltage divider to make the switch automatic, and the instructor builds and tests it with a real fan and a soldering iron.

**Prerequisites:** how NPN and PNP transistors switch, the inverting transistor pair from [ep10](../ep10-battery-charge-status-monitor/), and the voltage divider.

---

## 1. Why Saturation Matters: Power Loss and Heat

A transistor is **saturated** when it is switched fully on and the voltage between collector and emitter is close to zero: a few tens to a few hundred millivolts.

The power a transistor wastes is the voltage across it times the current through it:

$$P_{loss} = V_{CE} \times I_C$$

That power doesn't disappear. It turns into **heat** inside the transistor.

| State | $V_{CE}$ | $P_{loss}$ | Result |
|---|---|---|---|
| Saturated | ≈ 0 V | ≈ 0 W | Stays cool |
| Partly on | a few volts | $V_{CE} \times I_C$ (can be watts) | Heats up |

With a small LED load this barely matters, but with a motor, pump or lamp drawing 1–3 A, an unsaturated transistor gets hot quickly. **Saturating the transistor is how we keep it cool**, and it also lets the full supply voltage reach the load.

---

## 2. The Car Rule: Loads Are Always Grounded

In car manufacturing, every electrical part (headlights, indicator lamps, pumps, wiper motor, fans) has one terminal **always tied to the battery negative**, which mechanics call the **chassis** or **body**. You never switch the negative side.

To turn a load on, you switch its **positive** side with a transistor, a relay, etc. This is called **high-side switching**.

The load also needs the **full 12 V**. A 12 V pump that only gets 7 V won't work properly.

---

## 3. The Problem: NPN Above a Grounded Load

Start from the inverting pair of ep10, where the load (LED) sits on the collector of the output NPN, *above* the transistor (low-side switching). Simulated, that transistor saturates nicely: $V_{CE} \approx 111\ \text{mV}$.

Now follow the car rule: move the load **below** the NPN (load to ground, NPN between +12 V and the load). The LED still turns on and off, so the circuit "works", but the voltages tell a different story:

| Load | Base resistors | Voltage reaching the load (of 12 V) |
|---|---|---|
| LED | original | ≈ 7.8 V (≈ 4–5 V lost in the transistor) |
| LED | 10 kΩ | ≈ 9.6 V |
| LED | 1 kΩ | ≈ 11 V |
| LED | 100 Ω | ≈ 11.2 V |
| Motor (≈ 1.2 A) | original | ≈ 7 V |
| Motor (≈ 1.2 A) | reduced to ~10 Ω | ≈ 9 V (≈ 2 V still lost) |

**Why:** in this position the NPN acts as an **emitter follower**. Its emitter (the load voltage) can only rise to about 0.7 V *below* its base. As the load voltage rises, $V_{BE}$ shrinks and the transistor throttles itself. The base can't be pushed above the 12 V supply, so the load never gets the full 12 V.

Pumping more base current (smaller base resistors) helps a little with an LED, but with a 1.2 A motor about 2 V stays across the transistor:

$$P_{loss} \approx 2\ \text{V} \times 1.2\ \text{A} \approx 2.4\ \text{W}$$

That's a lot of heat for a transistor, and the tiny base resistors now carry large currents and heat up too.

---

## 4. Reading Heat from a Datasheet

How hot will the transistor get? Datasheets sometimes help:

| Transistor | Package | Max power $P_{tot}$ | Derating |
|---|---|---|---|
| TIP31 / TIP32 | TO-220 (metal tab) | 40 W | 0.32 W/°C |
| BC547 | TO-92 (plastic) | 500 mW | not given |
| 2N2222 | TO-18 (metal can) | — | 2.28 mW/°C |

The instructor reads the **derating factor** as "how much power raises the temperature by 1 °C", starting from room temperature (25 °C). For the TIP31, $1/0.32 \approx 3.1\ ^\circ\text{C/W}$, so wasting 1 W raises it about 3 °C (25 → ≈ 28 °C).

> **My note:** strictly, $1/\text{derating}$ is the junction-to-case thermal resistance ($R_{\theta JC}$). Without a heatsink, the real rise is much larger. Either way, the point stands: it is only an **estimate**.

When no thermal data is given (BC547), just make sure $V_{CE} \times I_C$ stays well under the maximum power. The safest approach: **make sure the transistor saturates** so the loss is as small as possible.

---

## 5. The Solution: The Driver Transistor Circuit

For a grounded load, use a **PNP** on the high side: emitter to +12 V, collector to the load. A PNP saturates when its base is pulled to **0 V**, and pulling a base to ground is easy. A small NPN can do it.

**Connections:**

- **Load:** PNP collector → load → ground (chassis)
- **PNP:** emitter to +12 V; a resistor from its base to +12 V (keeps it off)
- **Driver NPN:** collector → resistor → PNP base; emitter to ground
- **Input:** switch / sensor → base resistor → NPN base

**How it works:**

- **Switch open:** the NPN is off, the PNP base is pulled up to 12 V → $V_{BE} = 0$ → **PNP off, load off**.
- **Switch closed:** the NPN saturates (simulated collector ≈ 2.5 mV), pulling the PNP base toward 0 V → **PNP saturates, load gets ≈ 12 V**.

Unlike the ep10 pair, this one is **not inverting**: both transistors turn on and off together.

**Simulated results (from the video):**

| Load | Base resistors | Load voltage |
|---|---|---|
| LED | 10 kΩ / 10 kΩ | ≈ 11.6 V |
| Lamp + motor | 10 kΩ / 10 kΩ | ≈ 11 V |
| Lamp + motor (≈ 1.5 A) | reduced to 1 kΩ | ≈ 11.2 V |

The PNP passes almost the full 12 V even at 1.5 A, with ordinary resistor values.

### Adding an indicator LED

Put an **LED + resistor** in the PNP's base path (between the PNP base and the NPN collector). When the NPN switches on, the PNP base current flows through the LED, so it lights exactly when the load is on. It's a free "fan is running" signal, and the load still gets ≈ 11.25 V.

### A second form (not for grounded loads)

The roles can be swapped: a **PNP drives an NPN**, with the load on the NPN's collector (above the NPN). The PNP turns on when its input is pulled to **ground**, then sends current into the NPN base.

This is useful when:

- the control signal is **ground-active** (a switch or sensor that connects to 0 V instead of to +12 V), and
- you want to push the output NPN into saturation **quickly and firmly**.

It doesn't follow the car rule, because the load isn't grounded.

---

## 6. Design Task: Automatic Cooling Fan

**Brief:** design a circuit that turns a car's 12 V cooling fan on when it gets hot and off when it cools down. The fan must stay connected to the battery negative.

**Plan:**

- The fan is grounded → a **PNP high-side switch**.
- To saturate the PNP → the **driver-transistor** circuit (NPN driving PNP).
- Replace the manual switch with a **temperature sensor** that makes the decision.

### The sensor: an NTC thermistor in a voltage divider

A **thermistor** is a resistor whose value changes with temperature. This one is **NTC**: hotter → lower resistance. The instructor measured his:

| Condition | Resistance |
|---|---|
| Room temperature (≈ 25 °C) | ≈ 7.5–8 kΩ |
| Heated | drops toward ≈ 100 Ω |

A resistance change alone can't switch a transistor, so the thermistor goes in a **voltage divider**:

- **Top ($R_1$):** thermistor, from +12 V to the midpoint
- **Bottom ($R_2$):** fixed resistor, from the midpoint to ground
- **Midpoint** → NPN base (the driver)

As the thermistor heats up, $R_1$ falls, so a larger share of the 12 V appears across $R_2$, and the NPN base voltage rises until it turns on.

### Calculating $R_2$

An NPN needs about 0.6–0.7 V on its base in theory, but in practice it can start conducting near 0.5 V. To be sure the fan stays **off** at room temperature, aim for **0.4 V** when the thermistor is 8 kΩ.

$$V_{out} = V_{in} \cdot \frac{R_2}{R_1 + R_2}$$

$$0.4 = 12 \cdot \frac{R_2}{8\ \text{k}\Omega + R_2} \quad\Rightarrow\quad R_2 = \frac{0.4 \times 8\ \text{k}\Omega}{12 - 0.4} \approx 276\ \Omega$$

(The video gives ≈ 273 Ω.) This is roughly the *boundary* value. Since the thermistor may sit at 7.5 kΩ instead of 8 kΩ at room temperature, the instructor picks a lower, safer value: **$R_2$ = 100 Ω**. The important lesson: the answer is in the **hundreds of ohms**, not 10, 20 or 30 kΩ.

> **My check:** with $R_2$ = 100 Ω, the base sees $12 \times \frac{100}{8100} \approx 0.15\ \text{V}$ at room temperature (well off). It reaches ≈ 0.6 V when $R_1$ falls to about $\frac{12 \times 100}{0.6} - 100 \approx 1.9\ \text{k}\Omega$, so the fan should switch on once the thermistor drops below ≈ 2 kΩ.

### Component values (hardware build)

| Part | Value |
|---|---|
| Supply | 12 V |
| Fan | 12 V DC cooling fan (to ground) |
| High-side PNP | **BD140** (chosen for its high current rating) |
| Driver NPN | **BC547B** |
| Thermistor $R_1$ | NTC, ≈ 8 kΩ at 25 °C |
| $R_2$ (divider bottom) | **100 Ω** (≈ 276 Ω calculated as the boundary) |
| Other resistors | 1 kΩ, plus a few-kΩ resistor in the driver stage |
| Indicator | Red LED (lights with the fan) |

### Results

**Simulation (from the video):** at low temperature the divider gives about 0.5 V, so neither the fan nor the LED is on. When the temperature is raised, the divider voltage rises to about 2 V, and the LED and fan switch on.

**Hardware test:** the instructor brings a soldering iron slowly toward the thermistor. Once it is hot enough, the **red LED and the fan turn on**. When the iron is moved away and the sensor cools, they **turn off**. When the fan is pointed straight at the sensor, it switches on and then quickly off again, because it cools its own sensor.

In a car, the sensor would sit on the engine body, so the fan runs only while the engine is above a set temperature.

---

## What I Learned

- **Saturation = cool transistor.** $P = V_{CE} \times I_C$: with $V_{CE} \approx 0$ almost no power becomes heat.
- In cars, loads are **always grounded to the chassis**. Switching happens on the **positive (high) side**.
- An **NPN above a grounded load** behaves like an emitter follower. It can't saturate, wastes several volts (and watts), and smaller base resistors only partly fix it.
- **Datasheets** give $P_{tot}$ and sometimes a derating factor for estimating temperature rise. It's an estimate, so the real fix is saturation.
- The **driver transistor** circuit: a small NPN pulls a PNP's base to 0 V, so the PNP saturates and passes ≈ 11–11.6 V even at 1.5 A.
- An LED in the PNP base path is a free "load on" indicator.
- Swapping roles (PNP driving NPN) suits **ground-active** input signals.
- Thermistor + fixed resistor = a **voltage divider** that turns temperature into a base voltage. Size the fixed resistor so the base stays below ≈ 0.4–0.5 V at the "off" temperature.
- With the fan blowing on its own sensor, the circuit turns on and off rapidly, because nothing gives it a gap between the on and off temperatures.

### Next to try

- Build the fan controller in Circuit Wizard (BD140, BC547B, NTC thermistor, $R_2$ = 100 Ω) and record the divider voltage and fan voltage while sweeping the temperature.
- Measure $V_{CE}$ of the BD140 and compare the NPN-above-load and PNP-driver versions with a ≈ 1.2 A motor: how much power does each waste?
- Find the thermistor resistance at which my simulated fan switches on, and compare with my ≈ 2 kΩ estimate.
- Try $R_2$ = 276 Ω vs 100 Ω and compare the switch-on temperature.
