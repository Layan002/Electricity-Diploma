# Ep22 – The Flip-Flop Capacitor as an Oscillator, and Designing a Status Indicator

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 22- المكثف القلاب كمذبذب وتصميم مؤشر حالة (The flip-flop capacitor as an oscillator, and designing a status indicator)](https://www.youtube.com/watch?v=nx2RcUuGxUg&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=160) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

This episode introduces an idea the instructor calls the **flip-flop capacitor** (المكثف القلاب). It's an ordinary capacitor, but its charge **flips sign** (from + to −) depending on what its two plates are connected to. That flip can switch other circuits **on and off**.

> "Flip-flop capacitor" is the instructor's own teaching name. It isn't a textbook term, but it makes cheap oscillator circuits much easier to understand.

The episode has five parts:

1. **The idea**: a capacitor's voltage is **relative**. Push one plate up and the other plate goes up too.
2. **The flip**: if one plate is **held** at a fixed voltage, the capacitor has to change (even reverse) its charge instead.
3. **A one-capacitor oscillator** (an NPN + PNP flasher) built on that idea, as found in many cheap products.
4. **Design task:** a **low-battery flasher** for a UPS that only starts when the 12 V battery falls to **8 V**.
5. **Recognising oscillators** in other people's schematics, and combining them with "decision" circuits.

You need a good grasp of **transistor switching** and **capacitor charging** first. The two-transistor flasher in [ep23](../ep23-random-selection-circuit-oscillator/) builds directly on this episode.

---

## 1. A Capacitor's Voltage Is Relative

### The circuit

- **5 V** battery → **1 kΩ** resistor → **1 µF** capacitor → bottom plate to **ground**.
- A voltmeter from the top plate to ground.

This is just an RC charging circuit. At first the current is high, then it falls toward zero as the capacitor charges. In the end the capacitor holds **5 V**.

### Lift the bottom plate

Now the bottom plate is connected to a small source instead of ground:

| Bottom plate | Voltage across the capacitor | Top plate (to ground) |
|---|---|---|
| 0 V | 5 V | 5 V |
| 1 V | 5 V | **6 V** (5.97 V in the simulation) |
| 2 V | 5 V | 7 V |
| 3 V | 5 V | 8 V |

The capacitor still holds its 5 V **between its plates**. Its top plate is just 5 V above **whatever the bottom plate is at**:

$$
V_{top} = V_{bottom} + V_C
$$

So the charge on a capacitor is always **relative to its other plate**, not to ground.

---

## 2. The Flip: When One Plate Is Held

### Top plate held at 5 V

Now a battery also **forces** the top plate to stay at 5 V. With the bottom plate at 0 V, the capacitor charges to 5 V as before.

Lift the bottom plate to 1 V. On its own, the capacitor would push the top plate to 6 V, but the battery won't allow it. Both ends are forced: top = 5 V, bottom = 1 V. The only way to satisfy both is for the capacitor to **discharge** to:

$$
V_C = 5\ \text{V} - 1\ \text{V} = 4\ \text{V}
$$

The simulation showed exactly that: **4 V** across the capacitor.

### Top plate held at 0.7 V → the charge flips

Repeat with a **0.7 V** source on the top plate:

1. Bottom plate at 0 V → the capacitor charges to **+0.7 V**.
2. Bottom plate lifted to 1 V → the capacitor "wants" to make the top plate 1.7 V, but it's held at 0.7 V.
3. To satisfy both ends, the capacitor must go to:

$$
V_C = 0.7\ \text{V} - 1\ \text{V} = -0.3\ \text{V}
$$

The simulation showed **−299 mV**. The charge went from **positive to negative**. That's the **flip-flop capacitor**.

Flipping the charge also means the **current reverses**: the capacitor was taking current, now it gives current back (or the other way round).

### The rules of the flip-flop capacitor

1. **One plate is always tied to a fixed-voltage point.** That can be a battery or, as below, a transistor's base–emitter junction.
2. **The other plate's voltage depends on something else**: a switch, or a resistor with current flowing through it (which creates a voltage by Ohm's law).
3. When the second plate moves, the capacitor has to adjust its charge, and it may **flip** to the opposite sign.

### Replacing the battery with a transistor

A transistor's base–emitter junction does the same job as the 0.7 V battery: once the transistor is on, it **clamps** its base at about 0.7 V.

- Simulation: base held at ≈ **750 mV**.
- Lift the bottom plate by 1 V → the capacitor's charge goes **negative**, but the base stays at ≈ 0.7 V.

### Watching it switch an LED

With an LED in the transistor's collector, the instructor kept toggling the bottom plate between 0 V and 1 V by hand:

- Each time the bottom plate **dropped back to 0 V**, the LED **went out for a moment**, then came back.
- When the bottom plate **rose** to 1 V, the LED stayed on.

Why: when the bottom plate drops, the capacitor's charge goes from negative back toward +0.7 V and passes **through 0 V**. During that moment the capacitor pulls current away from the base, so the transistor can't get enough base current and **switches off briefly**. When the bottom plate rises, the transistor is still being fed from the base resistor, so nothing happens.

Later the bottom plate won't be switched by hand. It will be driven by **another part of the circuit** that goes high and low by itself. That gives an oscillator.

---

## 3. The Flip-Flop Capacitor Oscillator (Simple Flasher)

### The circuit

- **9 V** supply.
- **100 kΩ** from +9 V to the base of an **NPN** (Q1). This resistor charges the capacitor.
- Q1's collector drives the base of a **PNP** (Q2).
- Q2 feeds an **LED** (and optionally a **buzzer** in parallel), and the current returns to ground through a small **27 Ω** resistor.
- A **capacitor** (33 µF in the hardware) from Q1's base to the node above the 27 Ω resistor.
- A second, small capacitor that helps at low supply voltages (see below).

The flip-flop capacitor has one plate on **Q1's base** (held at ≈ 0.7 V once Q1 conducts) and the other plate on the **27 Ω node** (0 V when no current flows, ≈ 1 V when the LED is on).

### How it oscillates (as explained in the video)

1. **Start:** Q1 is off, so Q2 is off and no current flows through the 27 Ω. The bottom plate sits at **0 V**, just like the "grounded" case in part 1.
2. The **100 kΩ** slowly charges the capacitor and raises Q1's base.
3. At ≈ 0.7 V Q1 turns on. It pulls Q2's base low, so the **PNP turns on** and current flows through the LED and the 27 Ω.
4. By Ohm's law the 27 Ω now has a voltage across it: about **0.9–1 V** at the bottom plate.
5. Now the top plate is held at 0.7 V and the bottom plate is at ≈ 1 V. The capacitor **flips** its charge to negative, which switches Q1 **off** for a moment, so the LED goes out.
6. The 100 kΩ charges the capacitor again, and the cycle repeats.

**My note (not from the video):** The usual textbook description of this NPN–PNP flasher is a bit different, but it ends up at the same picture. When the LED turns on, the jump at the bottom plate first pushes **more** current into Q1's base (positive feedback, so it snaps on). The capacitor then charges through the base until that current fades and the pair can't stay on. When the LED current stops, the bottom plate **drops** by ≈ 1 V and drags Q1's base **below 0 V**. Q1 stays off until the 100 kΩ pulls the base back up to 0.7 V. This is the same "drop → flip → brief OFF" effect seen in part 2, and it explains why the OFF time is so much longer than the ON time.

### What the oscilloscope showed

The scope across the capacitor (hardware, 33 µF):

- The voltage is **negative almost all the time**.
- It only crosses **above 0 V** for a short moment. That's when the LED flashes. Then it is pushed back down to negative.
- Frequency ≈ **1.4 Hz**.

So the LED gives **short flashes with long gaps**: the ON time is much shorter than the OFF time.

### Changing the speed

The flash rate depends on how fast the capacitor charges and discharges, which is set by three parts:

| Part | Value in the video | Effect of increasing it |
|---|---|---|
| Capacitor | 33 µF (hardware), 100 nF / 10 nF (simulation) | Slower flashing |
| Charging resistor | 100 kΩ | Slower flashing |
| Emitter-side resistor | 27 Ω | Must stay below ≈ **40–50 Ω**; larger values stopped the circuit working |

Tried in the hardware:

| Capacitor | Result |
|---|---|
| 1 µF | Fast flashing (higher frequency) |
| 33 µF | ≈ 1.4 Hz |
| 220 µF | Very slow: one flash after a long wait (like the "armed" LED in some cars) |

A rough guide from the end of the video: use **nanofarads** (e.g. 100 nF) for fast switching, and **microfarads** for delays of seconds.

**Simulation tip from the video:** Circuit Wizard shows the flashing poorly with large capacitors. The instructor used **10 nF – 100 nF** and a simulation time step of **100 µs** so the flashing is visible without waiting.

### Capacitor polarity

The instructor put the 33 µF in without worrying about polarity, and it worked. **My note:** the capacitor sees only about ±1 V here, which an electrolytic usually tolerates. A non-polarised capacitor would be the safe choice in a product.

### The second capacitor (speed-up)

Removing the second, small capacitor made almost no difference at 9 V. Its job shows at **low supply voltages**:

| Supply | Without it | With it |
|---|---|---|
| 9 V, 6 V, 5 V | Works normally | Works normally |
| 4 V | Flashing changes / weaker | Back to normal |
| 3 V | Doesn't work | Doesn't work |

Why: an **empty** capacitor draws a **large current** when it suddenly starts charging. When the transistor turns on and puts 0 V on one side, this capacitor pulls a burst of current through the transistor. That **speeds up switching on**. It only improves the circuit; with a stable supply it isn't needed.

### Where you see this circuit

- The small LED that blinks all night in some **cars** (alarm armed / battery present), sometimes once every ~30 s.
- **Bicycle rear lights** powered by a pedal dynamo: the flashing warns drivers behind.
- With a **buzzer** in parallel with the LED: the short beeps some devices make when their supply voltage is low.

It's a very cheap oscillator: one capacitor, two transistors and a few resistors. Chinese manufacturers use it a lot.

---

## 4. Design Task: Low-Battery Flasher for a UPS

### The brief

A UPS factory wants a **low-battery warning**:

- The UPS battery is **12 V**.
- When it falls to **8 V**, an LED (and buzzer) should **start flashing**, telling the user to stop using it before the battery is damaged.
- Above 8 V the indicator must be **completely off**.
- Use **basic components only**: transistors, resistors, capacitors, LEDs, diodes. **No relays.**

There are many valid solutions. The instructor's approach: find the **heart of the oscillator** and switch it off while the voltage is high.

### Step 1 – Kill the capacitor with a transistor

The capacitor is what makes the circuit oscillate. Put an **NPN transistor in parallel with the capacitor**:

- **Transistor on** → it takes the charging current and sends it to ground. The capacitor can't charge, so the **oscillator stops**.
- **Transistor off** → it's as if it weren't there, and the oscillator **runs normally**.

The simulation first tested this with a switch on the transistor's base: base floating → flashing; base driven → flashing stopped; released → flashing resumed.

### Step 2 – Drive it from a voltage divider

Replace the switch with a **voltage divider** from the battery. We want the transistor to be **on above 8 V** and **off below 8 V**, so the base should be at the threshold when the battery is exactly 8 V.

Choose $R_1 = 100\ \text{k}\Omega$ (large, so the divider wastes little current). Then:

$$
V_B = V_{bat} \cdot \frac{R_2}{R_1 + R_2}
\quad\Rightarrow\quad
R_2 = R_1 \cdot \frac{V_B}{V_{bat} - V_B}
$$

The instructor's advice: in practice, design for about **0.5 V**, not 0.7 V, because many transistors already start conducting at ≈ 0.5 V.

| Target $V_B$ at 8 V | $R_2$ |
|---|---|
| 0.5 V | $100\ \text{k}\Omega \times 0.5 / 7.5 \approx 6.7\ \text{k}\Omega$ |
| 0.7 V | $100\ \text{k}\Omega \times 0.7 / 7.3 \approx 9.6\ \text{k}\Omega$ |

He chose **6.5 kΩ** in the simulation:

| Battery | Base voltage | Transistor | Indicator |
|---|---|---|---|
| 12 V / 9 V | ≈ 0.5 V or more | On | Off (no flashing) |
| ≈ 8 V | Right at the edge | Turning off | **Starts flashing** |
| below 8 V | Lower | Off | Flashing |

### Step 3 – Hardware with a trimmer

In the real circuit he used **100 kΩ + a variable resistor** (trimmer) for the bottom of the divider, and an NPN across the capacitor (the two wires go to the capacitor's two ends). A buzzer was added in parallel with the LED.

- Lowering the bench supply 12 → 11 → 10 → 9 → 8 V: nothing. At **7 V** it started flashing and beeping.
- He set the supply to **8 V** and turned the trimmer until it **just started flashing**.
- After calibration: **9 V → off**, **8 V → flashing**.

The trimmer is the practical answer: you don't need an exact calculation, you **calibrate** at the threshold voltage.

**My notes (not from the video):** the base current slightly loads the divider, and a transistor's $V_{BE}$ drops by about 2 mV/°C, so the threshold moves with temperature. Also, with no hysteresis, the LED may flicker weakly when the battery sits right at 8 V. A Zener in series with the base would make the threshold sharper.

The instructor's point: with **one transistor and two resistors** added to the flasher, we have the **cheapest possible** low-battery alarm when no programming is wanted. It's a circuit worth memorising.

---

## 5. Recognising Oscillators in Schematics

### Latch vs. oscillator

Both have two transistors where each **collector feeds the other's base**. The difference is what sits in those links:

| Collector → base links | Circuit |
|---|---|
| Resistor + resistor | **Latch** (ep18) |
| Resistor + **capacitor** | **Oscillator** |
| **Capacitor + capacitor** | **Oscillator** (the classic flasher of ep23) |

The two transistors can be NPN + PNP (as in this episode) or both the same type, and the **load can be on top or at the bottom**. When you see a capacitor in a collector-to-base link, think "oscillator" straight away; its frequency depends on those R and C values.

### Oscillator + "decision" circuit

Many market circuits are an **oscillator block** plus a **decision block** that decides when it may run:

| Product | Decision circuit | How it controls the oscillator |
|---|---|---|
| Low-battery alarm (another version) | Transistor + two resistors with different values | Shorts the base of the oscillator's main transistor to ground while the voltage is high; releases it below the threshold |
| **Fridge door left open** alarm | **LDR** (light enters when the door opens) + a slowly charging capacitor | Gives you time to close the door; if it stays open, the capacitor charges and a transistor lets the **buzzer** oscillator run |
| Li-ion **3.7 V** low-battery alarm | Divider + an extra **inverting transistor** | The extra transistor flips the logic (short ↔ open) |

There are a hundred ways to build each function, but the pattern is the same: **oscillator + decision circuit**.

### Limits of this oscillator

This is a **simple, non-professional** oscillator. It gives short flashes or short beeps, but you **can't set the ON and OFF times independently**. Later episodes cover oscillators where you can. It's still widely used for **on/off switching**, e.g. with a 100 nF capacitor for fast switching.

---

## Summary of Values from the Video

| Part | Item | Value |
|---|---|---|
| Idea | RC demo | 5 V, 1 kΩ, 1 µF |
| Idea | Bottom plate lifted 1 V (free top) | 5 V → 6 V (5.97 V) |
| Idea | Top held at 5 V, bottom → 1 V | Capacitor → 4 V |
| Idea | Top held at 0.7 V, bottom → 1 V | Capacitor → −0.3 V (−299 mV) |
| Idea | Transistor base clamp | ≈ 0.75 V |
| Flasher | Supply | 9 V |
| Flasher | Charging resistor | 100 kΩ |
| Flasher | Emitter-side resistor | 27 Ω (keep below ≈ 40–50 Ω) |
| Flasher | Bottom-plate voltage when LED on | ≈ 0.9–1 V |
| Flasher | Capacitor (hardware) | 33 µF → ≈ 1.4 Hz |
| Flasher | Other capacitors tried | 1 µF (fast), 220 µF (very slow) |
| Flasher | Capacitor (simulation) | 10 nF / 100 nF, time step 100 µs |
| Flasher | Lowest supply | Works at 4 V with the speed-up capacitor, fails at 3 V |
| UPS design | Battery | 12 V, alarm at 8 V |
| UPS design | Divider (simulation) | 100 kΩ / 6.5 kΩ (≈ 0.5 V on the base at 8 V) |
| UPS design | Divider (hardware) | 100 kΩ / trimmer, calibrated at 8 V |
| UPS design | Before calibration | Started flashing at 7 V |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add simulations of the flip-flop capacitor demo, the flasher and the UPS low-battery indicator here.

---

## What I Learned

- A capacitor's voltage is **between its plates**. Move one plate and the other follows: $V_{top} = V_{bottom} + V_C$.
- If one plate is **held** (by a battery or a transistor's B–E junction) and the other moves, the capacitor must change its charge, and can **flip** it to negative. This is the **flip-flop capacitor**.
- When the moving plate **drops**, the held side is pulled below 0 V for a moment and the transistor switches off. That brief OFF, repeated, is an **oscillator**.
- The NPN + PNP flasher uses **one capacitor**: short ON flashes, long OFF gaps. R and C set the speed; the emitter-side resistor must stay small (< ≈ 40–50 Ω).
- A small extra capacitor can **speed up switching** and let the circuit run at lower supply voltages.
- To **disable an oscillator**, put a transistor across its timing capacitor (or short the main transistor's base). Drive that transistor from a **voltage divider + trimmer** to make a threshold-controlled indicator.
- Design the divider for ≈ **0.5 V**, not 0.7 V, and **calibrate** with a trimmer at the real threshold.
- In schematics: collector-to-base links with **R + R = latch**, with **R + C or C + C = oscillator**. Market circuits often combine an **oscillator** with a **decision circuit** (divider, LDR, delay).

### Next to try

- Rebuild the part 2 demo (0.7 V source, then a transistor) and record the capacitor going from +0.7 V to −0.3 V.
- Simulate the NPN + PNP flasher with 100 nF and plot the capacitor voltage: check that it's negative most of the time, and measure the ON/OFF ratio.
- Change the 27 Ω to 47 Ω and 68 Ω to find where it stops oscillating.
- Build the UPS indicator, sweep the supply from 12 V down to 6 V, and find the exact start voltage with 6.5 kΩ, 6.8 kΩ and 10 kΩ.
- Compare this one-capacitor flasher with the two-capacitor astable in [ep23](../ep23-random-selection-circuit-oscillator/).
