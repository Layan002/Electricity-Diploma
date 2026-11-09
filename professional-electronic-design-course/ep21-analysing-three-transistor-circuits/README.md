# Ep21 – Analysing Three Transistor Circuits to Learn From Them

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 21- تحليل ثلاث دوائر ترانزستور لنتعلم منها (Analysing three transistor circuits to learn from them)](https://www.youtube.com/watch?v=vSdBvg5xxMc) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

This episode doesn't design a new circuit. Instead it **analyses three circuits that already exist** in cars, shops and cheap accessories, using only what Levels 1 and 2 taught: transistor switching, Zener thresholds, RC delays and latches.

You need the earlier episodes first, especially the **RC delay** ([ep12](../ep12-car-fan-delayed-turn-off/)) and the **transistor latch** ([ep18](../ep18-transistor-latch-with-delay/), [ep19](../ep19-smart-mailbox-letter-detector/)).

The method for each circuit:

1. Look at the schematic and **try to analyse it yourself first** (pause the video). 20 %, 50 % or 70 % is fine; the point is to find your weak spots.
2. Follow the current: which part turns on **first**, what it switches next, and what changes if a switch is **open** or **closed**.
3. Check your reasoning in the **Circuit Wizard** simulation.

| # | Circuit | Where you find it | Key idea |
|---|---|---|---|
| 1 | Courtesy light with delayed turn-off | Car interior (dome) light | RC delay that switches a transistor pair **off** |
| 2 | Three-LED battery level indicator | Battery testers, car accessories, shops | Zener thresholds + transistors that **short out** an LED |
| 3 | Solar "engine" (charge-and-fire) | Solar dancing toys, nodding figures | Big capacitor + Zener trigger + **latch** |

The big lesson: many "smart" gadgets that look programmed are really **a few transistors, Zeners and capacitors**. Manufacturers use them because they are cheaper than a microcontroller.

---

## 1. Car Courtesy Light with Delayed Turn-Off

### The circuit

- **12 V** battery, a **lamp**, and a **normally closed (NC) door switch** that connects the lamp's lower end to ground.
- A small transistor circuit is connected **across the door switch**: three transistors, a capacitor and some resistors (100 kΩ to the first transistor's base, a **2.2 MΩ / 2.2 MΩ** divider to the third transistor's base).

The door switch works "backwards": when the door is **closed**, it pushes the switch **open**. When the door **opens**, the switch is released and **closes**.

In the simulation the push switch was replaced by an ordinary on/off switch, so each state can be held and studied.

### Door open → switch closed → lamp on

The current goes: 12 V → lamp → switch → ground. The lamp is on.

The switch is a **short circuit** across the whole transistor circuit, so **no current goes into it at all**. Current always takes the easiest path, and a closed switch is much easier than a path through resistors and junctions.

### Door closed → switch open → lamp stays on, then goes out

Now the lamp's lower end can only reach ground **through the transistor circuit**. Two things start at the same time:

**a) The lamp is kept on (fast):**

1. Current through the lamp flows through the **100 kΩ** resistor into the base of **Q1** and turns it on.
2. Q1 pulls the base of **Q2** low, which turns Q2 on.
3. Q2 connects the lamp's lower end to **ground**, taking over the job of the switch. The lamp stays on.

The voltage at the lamp's lower end doesn't drop all the way to 0 V. It must stay high enough to keep feeding Q1's base through the 100 kΩ, so Q1 and Q2 **keep each other on**.

**b) A timer starts (slow):**

1. Current also flows through a resistor into the **capacitor**, which starts charging from 0 V. How fast depends on the resistor and capacitor values: seconds, minutes, or longer.
2. The two **2.2 MΩ** resistors form a **voltage divider** that feeds the base of **Q3**:

$$
V_{B3} = V_C \cdot \frac{2.2\ \text{M}\Omega}{2.2\ \text{M}\Omega + 2.2\ \text{M}\Omega} = \frac{V_C}{2}
$$

3. Q3 needs $V_{BE} \approx 0.7\ \text{V}$, so the capacitor must reach:

$$
V_C = 2 \times 0.7\ \text{V} = 1.4\ \text{V}
$$

4. When Q3 turns on, it pulls **Q1's base to 0 V** → Q1 turns off → Q2 loses its drive and turns off → the lamp's lower end is no longer grounded → **the lamp goes out**.

So the whole thing is a **delayed turn-off** circuit for the lamp.

### Simulation (from the video)

- Switch closed: lamp on, no current anywhere in the transistor circuit.
- Switch opened: lamp still on. The capacitor climbs to about **1 V** (Q3's base ≈ 0.5 V), then to the point where Q3's base reaches ≈ **0.7 V**.
- As Q3 starts conducting, the lamp **dims gradually** and then turns off completely.

### In a real car

Open the door → the interior light comes on. Close the door → the light **stays on for about half a minute**, then **fades out**. That fade is exactly the gradual turn-off seen in the simulation. No programming is needed.

---

## 2. Three-LED Battery Level Indicator

### The circuit

- The supply is a **rechargeable battery** whose voltage moves between about **10 V** and **14–14.5 V**.
- Three LEDs show the level: **red**, **orange** and **green**.
- Each of the orange and green LEDs is in a branch with a **Zener diode** that sets the voltage it needs. The red LED is fed through a resistor.
- Two transistors decide which LED is allowed to light:
  - an **NPN** across the **red** LED,
  - a **PNP** across the **orange** LED and its Zener.

### How much voltage each branch needs

A branch with a Zener, an LED and a resistor only conducts when the supply covers all of them:

$$
V_{on} \approx V_Z + V_{LED} + V_R
$$

| Branch | Zener | LED | Resistor drop | Turns on at about |
|---|---|---|---|---|
| Green | 10 V | 2–3 V (green LEDs vary, take 3 V as the worst case) | 1–2 V | **14–15 V** |
| Orange | 9.1 V | ≈ 2 V | 1–2 V | **12–13 V** |

Below its threshold a Zener passes no current, so the whole branch behaves as if it were **removed**.

### Step by step as the battery voltage rises

**≈ 10 V – only red:**

- The green and orange branches are both off (not enough voltage for their Zeners).
- The orange branch carries no current, so the resistor that feeds the **NPN's base** sits at **0 V**. The NPN is off, as if it weren't there.
- The PNP's base and emitter are both at about 10 V. With no 0.7 V difference, the PNP is **off** too.
- So current flows through the resistor and the **red LED** only → **red on**.

**≈ 11.5–12 V – orange takes over from red:**

- The orange branch starts conducting (it began to glow at about **11.25 V** and was fully on around **11.5–12 V**).
- Its current flows through the resistor at the bottom of the branch, which builds up a voltage there (≈ **1.5 V** in the simulation). That turns the **NPN on**.
- The NPN sits **across the red LED**. When it turns on, it acts like a **short**: all the current goes through the NPN and the red LED gets nothing → **red off, orange on**.

**≈ 13.5 V – green takes over from orange:**

- The green branch starts conducting. Its current pulls the **PNP's base** below its emitter.
- Example from the simulation (with the green Zener changed to 11 V, see below): base ≈ **12.7 V**, emitter ≈ **13.4 V**. The difference is more than 0.7 V, so the **PNP turns on**.
- The PNP sits **across the orange LED and its Zener**. It shorts them out and sends the current through the branch resistor instead (that node rose to ≈ **6.38 V**) → **orange off, green on**.

It's the same trick as the NPN across the red LED, just done "from above". The instructor's rule from earlier episodes: **to switch something near the positive rail (high side), use a PNP**.

### Fixing the thresholds in the simulation

With the **10 V** Zener in the green branch, green came on at the same time as orange (around 12 V), so the levels overlapped. The instructor raised that Zener to **11 V**. Then:

| Battery voltage | LED on |
|---|---|
| below ≈ 11 V | 🔴 red |
| ≈ 12 V | 🟠 orange |
| ≈ 13.5 V and above | 🟢 green |

**Lesson:** the Zener values **set the switching points**. Change a Zener and you move the threshold.

This kind of indicator is sold in shops as a "smart" battery meter, but there is no software in it: just Zeners and transistors.

---

## 3. Solar Engine: Charge a Capacitor, Then Fire

### The circuit

- A small **solar cell** (in the simulation, a variable voltage source). Small cells give about **1.5 V**; slightly larger ones about **5–6 V**.
- A **diode**, so current only flows **one way**: from the cell to the capacitor, never back.
- A large **capacitor**: **4700 µF** or **10 000 µF**.
- A very small **motor** (runs on about **1–1.5 V** and very little current).
- A **resistor** and a **2.7 V Zener** feeding the base of an **NPN**.
- The NPN and a **PNP** connected **collector-to-base, base-to-collector**: a **latch** (from ep18).

### Why the big capacitor?

A solar cell's current depends on the light. Clouds pass, a car moves, the light flickers. The cell alone often can't run the load. So it slowly **stores charge in a big capacitor**, and the circuit spends that charge in **one short burst** later.

### When does it fire?

The **Zener** only conducts above **2.7 V**. Once it conducts, it holds 2.7 V and the NPN needs another 0.7 V on top:

$$
V_{trigger} \approx V_Z + V_{BE} = 2.7\ \text{V} + 0.7\ \text{V} \approx 3.4\text{–}3.5\ \text{V}
$$

1. The capacitor charges. Below ≈ 3.5 V nothing happens.
2. At ≈ 3.5 V the Zener conducts and the **NPN turns on**.
3. The NPN turns on the **PNP**, which feeds current back to the NPN's base: the pair **latches**.
4. The latch drives the **motor**, which drains the capacitor's charge.
5. Once the latch is on, the node at the bottom is pulled to about 0 V. The Zener now stops conducting, but that doesn't matter: the latch **keeps itself on**.
6. The motor keeps running while the capacitor empties. When the voltage drops too low to keep the latch on (the video says once it falls below ≈ 3.5 V), the latch releases and the motor stops.
7. The capacitor charges again, and the cycle repeats.

Result: in weak or broken light, the motor gives **short pulses**: move, stop, move, stop.

**My note (not from the video):** In a real latch, the voltage needed to **hold** it on is usually lower than the voltage needed to **trigger** it. That gap (hysteresis) is what lets the motor use more of the stored charge in one burst. I want to check in the simulation how low the capacitor voltage actually goes before the motor stops.

### A motor still draws current when it doesn't turn

A motor is basically a **coil of wire**. Even a small current flows through it. If the current is too weak, the motor just **doesn't produce enough torque to turn**, but current still passes. Many people think "no rotation = no current", which is wrong.

### Simulation (from the video)

| Source voltage | What happens |
|---|---|
| 3 V | Motor off. About **40 µA** flows through the resistor and Zener (Zener ≈ 2.6 V, base ≈ 3.2–3.3 V), not enough to fire. |
| 3.7 V | NPN and PNP both on and latched → **motor runs continuously**. |
| Source lowered | The capacitor drains until the voltage falls under the threshold → motor stops. |

### Where it's used

- **Solar dancing/nodding toys** on car dashboards: a small cell charges a **4700 µF** capacitor. Instead of a motor, the burst drives an **electromagnet coil**. The coil pushes/pulls a **permanent magnet** fixed to the toy, which gives it a small kick, so it keeps swaying "forever" in sunlight.
- **Compass demo** in the video: each time the cell is lit, the coil is magnetised and **pulls the compass needle**, then lets go once the capacitor (470 µF in that part) is empty.

A cell that gives only ~1.5 V can't run the load continuously, but by **collecting charge in a capacitor** it can run it for about a second at a time.

---

## Summary of Values from the Video

| Circuit | Item | Value |
|---|---|---|
| 1 | Supply | 12 V |
| 1 | Q1 base resistor | 100 kΩ |
| 1 | Q3 base divider | 2.2 MΩ / 2.2 MΩ (÷ 2) |
| 1 | Capacitor voltage to turn the lamp off | ≈ 1.4 V |
| 1 | Real car delay | ≈ 30 s, then fades |
| 2 | Battery range | ≈ 10 – 14.5 V |
| 2 | Green Zener | 10 V → changed to **11 V** |
| 2 | Orange Zener | 9.1 V |
| 2 | LED forward voltage | ≈ 2 V (green can be 3 V) |
| 2 | NPN base node when orange is on | ≈ 1.5 V |
| 2 | PNP base / emitter when green is on | ≈ 12.7 V / ≈ 13.4 V |
| 2 | Thresholds (11 V Zener) | red < 11 V, orange ≈ 12 V, green ≈ 13.5 V + |
| 3 | Solar cell | ≈ 1.5 V (small), 5–6 V (larger) |
| 3 | Storage capacitor | 4700 µF or 10 000 µF |
| 3 | Zener | 2.7 V |
| 3 | Trigger voltage | ≈ 3.4 – 3.5 V |
| 3 | Current below trigger (3 V) | ≈ 40 µA |
| 3 | Motor | ≈ 1 – 1.5 V, very small current |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add simulations of the three circuits here.

---

## What I Learned

- To analyse an unknown circuit: follow the current, decide what turns on **first**, and check each switch in both states.
- A closed switch (or a saturated transistor) is a **short** that takes all the current. Anything in parallel with it gets nothing.
- **Car courtesy light:** a transistor pair keeps the lamp grounded after the door switch opens, and an RC timer with a **÷ 2 divider** (1.4 V on the capacitor → 0.7 V on the base) turns the pair off later. The slow rise of the base voltage is why the light **fades** instead of snapping off.
- **Battery indicator:** each branch needs $V_Z + V_{LED} + V_R$ to light. A transistor **across** an LED switches it off by shorting it. Use an **NPN** low down and a **PNP** near the positive rail. The Zener values set the thresholds.
- **Solar engine:** a big capacitor collects charge from a weak source, a Zener sets the firing voltage ($\approx V_Z + 0.7$ V), and a **latch** keeps the load on until the capacitor is drained.
- A motor passes current even when it isn't turning; it just lacks the torque.
- Lots of cheap "smart" products work without any programming.

### Next to try

- **Circuit 1:** rebuild the courtesy light in Circuit Wizard, check which transistors are NPN or PNP, measure the delay, and change R and C to see how the delay scales with $RC$.
- **Circuit 2:** sweep the battery voltage from 10 V to 14.5 V and record the exact switching points with 10 V and 11 V green Zeners.
- **Circuit 3:** plot the capacitor voltage over several charge–fire cycles. Find the voltage where the motor **stops** and compare it with the trigger voltage (≈ 3.5 V).
- **Circuit 3:** try 1000 µF vs. 4700 µF vs. 10 000 µF and compare how long each burst lasts.
