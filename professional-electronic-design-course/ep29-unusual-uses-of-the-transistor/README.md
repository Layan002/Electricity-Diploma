# Ep29 – Unusual Uses of the Transistor

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 29- استخدامات غير اعتيادية للترانزستور (Unusual uses of the transistor)](https://www.youtube.com/watch?v=MTLRy4q-eSo&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=167) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 20 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

Normally a transistor's base, collector and emitter each go to their "usual" places. In other people's designs (especially inside ICs) you often see transistors wired in strange ways: base tied to collector, base grounded with the collector left open, or a transistor drawn with two emitter arrows. This episode is a tour of those **unusual connections**, so that when you meet them you know their job straight away. There are no long calculations here; the goal is recognition.

1. **Diode-connected transistor** (base tied to collector) → acts as a diode.
2. Using it to **lift a signal by 0.7 V**: class B / AB crossover distortion, small-signal current control, and "zero-offset" followers.
3. **Matched transistor in the same package** → temperature compensation.
4. **Diode-connected transistor + small resistor** → a fixed reference voltage, and from it a constant current source.
5. **V_BE multiplier** → an adjustable "multiple of 0.7 V".
6. **Reverse emitter–base junction as a Zener** (and why companies avoid it).
7. **Transistor with several emitter arrows** → emitter area ratio → temperature sensor (LM35).

---

## 1. The Diode-Connected Transistor

Connect the **base to the collector**. The base–emitter junction is a PN junction, so between the collector/base node and the emitter (ground) you now have about $V_{BE} \approx 0.7\ \text{V}$, just like a diode. Plot its V–I curve and it *is* a diode curve: almost no current until ≈ 0.7 V, then the current rises quickly.

**Why not just use a diode?**

- In a circuit (or an IC) full of transistors, it's easier to **copy one more transistor** than to make a different part.
- A diode-connected transistor **reacts to temperature the same way** as the other transistors in the circuit, so their changes track each other.

> **How to recognise it:** a transistor with its **base wired straight to its collector** = a diode (≈ 0.7 V).

---

## 2. Lifting a Signal by 0.7 V

### Class B / AB amplifier (crossover distortion)

A class B output stage uses two transistors: one amplifies the **positive** half of the wave, the other the **negative** half. Each one needs about 0.7 V on its base–emitter before it starts conducting. So around 0 V, while the signal is between −0.7 V and +0.7 V, **neither** transistor conducts and the output is cut. This flat step at every zero crossing is called **crossover distortion**.

**Fix:** put a diode (here, a **diode-connected transistor**) in each base path so the signal is **raised by 0.7 V** before it reaches the transistor. The transistor then subtracts its 0.7 V and the output starts exactly where the input does, with no dead zone. Using transistors instead of diodes keeps everything as "one kind of part" and gives the same temperature behaviour.

### Controlling a current with a tiny signal

Say you want to control a transistor's collector current with a small signal, for example ±10 mV. On its own, the signal would need to be **above 0.7 V** before the transistor reacts. A transistor placed before it **raises the signal by 0.7 V**, and the next transistor **subtracts 0.7 V**, so the small signal now controls the current directly.

$$
V_{B} = V_{signal} + V_{BE} \quad\Rightarrow\quad V_{E} = V_{B} - V_{BE} = V_{signal}
$$

### A follower with no 0.7 V loss

An emitter follower ([ep27](../ep27-emitter-follower-special-circuits/)) copies the input with more current, but its output is **0.7 V lower**. Put a **PNP follower first** (it raises the signal by 0.7 V) and an **NPN follower after it** (it subtracts 0.7 V). The two drops cancel: the output equals the input, with the current-driving ability of a follower.

**Simulation in the video** (20 V supply, 1 kΩ load):

| Input | Middle node (after PNP) | Output |
|---|---|---|
| 10 V | ≈ 10.7 V | ≈ 10 V |
| 6 V | — | ≈ 6 V |
| 3 V | — | ≈ 3 V |
| 8 V | — | ≈ 8 V |

If the load changes a lot, the output can move a little.

> **How to recognise it:** a "diode" transistor (or an opposite-type follower) just before a transistor = **compensates the 0.7 V drop** of the next stage.

---

## 3. Matched Transistor for Temperature Compensation

Example: a transistor stage on a **20 V** supply whose job is to keep a node at **10 V** (half the supply). The current into that stage splits between a **10 kΩ resistor + transistor** branch and a matching **10 kΩ resistor + diode-connected transistor** branch.

**Problem:** temperature changes a transistor's β and current gain. If the current rises or falls, the 10 V point drifts to, say, 9 V or 11 V.

**Block:** use a **second, identical transistor** with the **same resistor**, both in the **same package**, with the second transistor's base driving the first's. If heat makes one draw more current, the other changes in the same way and pushes back in the opposite direction, so the circuit settles back at its design point (10 V).

> **How to recognise it:** a **matching transistor connected to another transistor's base** (often in the same package) = **temperature / β compensation**.

---

## 4. Diode + Small Resistor → Reference Voltage → Constant Current

The diode-connected transistor's curve isn't perfectly flat above 0.7 V. As the current grows, $V_{BE}$ creeps up slowly: maybe 0.6x V, then 0.72, 0.73, 0.75 V. Some designers need a **truly fixed point** as a reference for the rest of the circuit.

**Block:** add a **small resistor** to the diode-connected transistor. When the supply rises, $V_{BE}$ rises a little and the current through the small resistor changes too. The two changes cancel, so the **collector voltage stays fixed** (≈ 0.6 – 0.7 V) no matter how the supply changes. This is a **reference voltage**.

Then the reference is used:

1. To **fix the base voltage of another transistor**. A fixed base voltage gives a fixed emitter current, so its collector current stays the same **whatever the voltage**: a **constant current source** (topic of ep32).
2. That constant current through a resistor gives **another fixed voltage**, which can be used as a second reference.

$$
V_{ref,2} = I_{const}\, R
$$

> **How to recognise it:** diode-connected transistor + small resistor feeding the base of another transistor = **reference voltage → constant current source**.

---

## 5. Block: V_BE Multiplier

- One diode-connected transistor → 0.7 V.
- Two in series → ≈ 1.4 V (or 1.2 – 1.3 V, depending on the parts).
- Need **2 V**, or an exact non-standard value? Use a **V_BE multiplier**.

**Block:** one transistor with a **resistor from collector to base** ($R_1$) and a **resistor from base to emitter** ($R_2$). $R_2$ always has $V_{BE}$ across it, and $R_1$ carries the same current (base current is small), so:

$$
V_{CE} \approx V_{BE}\left(1 + \frac{R_1}{R_2}\right)
$$

| $R_1$ | $R_2$ | $V_{CE}$ |
|---|---|---|
| 10 kΩ | 10 kΩ | ≈ 2 × $V_{BE}$ |
| 20 kΩ | 10 kΩ | ≈ 3 × $V_{BE}$ |
| 30 kΩ | 10 kΩ | ≈ 4 × $V_{BE}$ |

**Where it's used:** biasing **class B / AB amplifiers**. A plain diode might give 0.7 V while the output transistors actually need, say, 0.78 V, so some crossover distortion remains. With a V_BE multiplier, one resistor is a **trimmer**: you adjust it until the bias voltage matches the output transistors' $V_{BE}$ exactly and the distortion disappears.

> **How to recognise it:** a transistor with a **resistor divider from collector to emitter whose midpoint feeds its own base** = **V_BE multiplier** (adjustable "diode stack").

---

## 6. Reverse Emitter–Base Junction as a Zener

This one looks very wrong: **base to ground**, **collector not connected**, and the voltage applied to the **emitter**, so the emitter–base junction is **reverse-biased**.

The datasheet answers what happens. The BC547 lists **$V_{EBO}$ (emitter–base voltage) = 6 V**. The junction doesn't "drop" 6 V; it **breaks down and holds at about 6 V**. Apply 10 V through a resistor and the emitter sits at ≈ 6 V: the junction behaves like a **Zener diode**.

**Catch:** working the junction in reverse breakdown **permanently damages the transistor's β**. After that, it can no longer be used as a normal amplifying transistor. Some people still use it as a cheap voltage clamp, but many companies dislike it.

**Protecting against it:** companies often put a **normal diode across the emitter–base junction** (reverse-connected). Any reverse voltage is then clamped at ≈ 0.7 V by the diode, so it can't break down the junction.

### Zener + diode input protection

Sensitive inputs (a microcontroller, a processor, a computer port) can be damaged by over-voltage. A common protection is a pair of diodes/Zeners: for a 3.3 V input that suddenly receives 7 V, the Zener **clamps it to 3.3 V** and sends the rest to ground, and a negative voltage is clamped by the diode.

Some designs build this from **two transistors** instead of real Zeners: one diode-connected transistor (the diode) and one reverse emitter–base junction (the Zener). The instructor points to the **Fluke 77** multimeter schematic, which uses this trick to protect its processor. It's rare, but you'll find it in designs from the 1960s–70s by very skilled engineers.

> **How to recognise it:** base grounded, collector open, emitter used as the input = **Zener (≈ $V_{EBO}$)**. Paired with a diode-connected transistor = **Zener + diode protection**.

---

## 7. Block: Transistor with Several Emitter Arrows

A transistor drawn with **two (or more) emitter arrows** means its **emitter area** is larger than a reference transistor's, by any ratio (×1.5, ×10, ×20…). Datasheets often write it next to the symbol, e.g. "**Q19 has 10× the area of Q17**" or "10A".

If both transistors get the **same base drive**, you'd expect the same collector current. But the larger emitter changes the response: the current density is different, so the two react **differently to temperature**.

Put both on resistors and the **voltage difference** between them changes **with temperature**. That makes a temperature sensor.

**Where it's used:** the **LM35** temperature sensor (common with Arduino). It looks like a transistor, but inside it has two transistors with different emitter areas. Their difference becomes a voltage proportional to temperature, which is then buffered by a follower / op-amp stage with two **feedback resistors**. The resistor values are tuned so the output is exactly **10 mV per °C**.

*My note (standard result, not from the video):* for two matched transistors with emitter area ratio $N$ at equal currents,

$$
\Delta V_{BE} = \frac{kT}{q}\ln N
\qquad
N = 10,\ T = 300\ \text{K} \Rightarrow \Delta V_{BE} \approx 25.9\ \text{mV} \times 2.30 \approx 59.5\ \text{mV}
$$

It's directly proportional to absolute temperature $T$ ("PTAT"), which is why it works as a sensor.

> **How to recognise it:** two (or more) emitter arrows / "N×" or "10A" label = **scaled emitter area**, usually for temperature sensing or a bandgap reference.

---

## Block Cheat Sheet

| Unusual connection | What it is | Typical use |
|---|---|---|
| Base tied to collector | Diode (≈ 0.7 V) | Bias, level shift, temperature tracking in ICs |
| Diode-transistor before a stage / PNP follower before NPN follower | 0.7 V lift | Remove crossover distortion; small-signal current control; follower with no 0.7 V loss |
| Identical transistor on another's base, same package | Matched pair | Temperature and β compensation |
| Diode-transistor + small resistor | Fixed reference voltage | Fix another transistor's base → constant current source |
| Divider from collector to emitter, midpoint to own base | V_BE multiplier: $V_{BE}(1 + R_1/R_2)$ | Adjustable class AB bias |
| Base grounded, collector open, emitter as input | Zener (≈ $V_{EBO}$, e.g. 6 V for BC547) | Voltage clamp / protection (destroys β) |
| Diode across E–B | Reverse-voltage protection | Protects the transistor's E–B junction |
| Multiple emitter arrows / "10×" | Scaled emitter area | Temperature sensor (LM35), references |

---

## Key Values

| Item | Value |
|---|---|
| Diode-connected transistor | ≈ 0.7 V (rises slowly with current: ≈ 0.6x → 0.75 V) |
| Two in series | ≈ 1.2 – 1.4 V |
| Zero-offset follower sim | 20 V supply, 1 kΩ load: 10 V in → 10.7 V middle → 10 V out |
| Temperature-compensated stage | 20 V supply, 10 V midpoint, 10 kΩ resistors |
| V_BE multiplier | 20 k / 10 k → 3×, 30 k / 10 k → 4× |
| BC547 $V_{EBO}$ | 6 V |
| Input protection example | 3.3 V input clamped against 7 V |
| Emitter area example | Q19 = 10 × Q17 |
| LM35 output | 10 mV / °C |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll start with the PNP + NPN zero-offset follower and the V_BE multiplier, since they are the easiest to check with simple parts.

---

## What I Learned

- A transistor with its **base on its collector** is just a diode, chosen because it's easy to copy in an IC and tracks the other transistors' temperature.
- "Raise by 0.7 V, then lose 0.7 V" is a recurring trick: it removes **crossover distortion**, lets a tiny signal control a current, and makes a follower with **no offset**.
- A **matched transistor in the same package** cancels temperature and β drift.
- Diode-transistor + small resistor = **reference voltage**, which leads to a **constant current source**.
- **V_BE multiplier:** $V \approx V_{BE}(1 + R_1/R_2)$, with a trimmer to bias class AB stages exactly.
- The **reverse emitter–base junction** breaks down like a ≈ 6 V Zener, but it ruins β. A diode across E–B protects against it.
- **More emitter arrows = larger emitter area.** Two transistors with different areas give a temperature-dependent voltage difference, which is the core of the **LM35**.

### Next to try

- Simulate the PNP → NPN follower with inputs 3, 6, 8, 10 V on a 20 V supply and compare with a single NPN follower (expect a 0.7 V gap).
- Build a V_BE multiplier with 10 k / 20 k / 30 k on top and 10 kΩ below, and check 2×, 3×, 4× $V_{BE}$.
- Build a class B push-pull stage, look at the crossover distortion, then fix it with two diode-connected transistors and then with a V_BE multiplier.
- Check whether Circuit Wizard models the emitter–base breakdown of a BC547 at ≈ 6 V.
- Read the LM35 datasheet's internal block diagram and find the two scaled transistors.
