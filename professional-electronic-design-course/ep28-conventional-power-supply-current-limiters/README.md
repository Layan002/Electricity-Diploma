# Ep28 – Designs for a Conventional Power Supply with Current Limiters

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 28- تصميمات لبورسبلاي تقليدي مع محددات التيار (Designs for a conventional power supply with current limiters)](https://www.youtube.com/watch?v=KHDRfvVpfYg&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=166) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 16 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

[Ep27](../ep27-emitter-follower-special-circuits/) turned the emitter follower into a simple **5 V linear regulator** (Zener on the base, follower copies it minus 0.7 V). It worked, but the output wasn't exact: under a heavy load it could sag to about **4.5 V**. A company selling regulators keeps improving **accuracy** and **protection**, and this episode follows those improvements block by block:

1. **Feedback with an op-amp** → a much more accurate output.
2. **Op-amp + follower** → accurate *and* high current.
3. Simulation: follower alone vs. follower with feedback.
4. **Current limiter** (sense resistor + transistor).
5. **Over-voltage protection** (SCR "crowbar" + fuse, or a non-latching version).
6. **Foldback** current limiting, for safer short circuits.
7. Putting it together: boosting a **7805**.
8. **Transistors in parallel**: BJTs (thermal runaway, emitter resistors) vs. MOSFETs.
9. Recognising all of these blocks inside the **LM317** and **7805**.

The theme of Level 3 again: learn to see each group of parts as a **block** with a known job, so you don't have to ask "why is this resistor here?" for every part.

---

## 1. Why Feedback?

Suppose the output is 5 V with no load, then a load pulls it down to 4.5 V. Something has to **notice** the drop and push more current to bring the output back to 5 V. That is **feedback**: you **monitor the output** and use what you see to **correct the input**.

Feedback can be made from resistors alone, a resistor + transistor, or an **op-amp**. The op-amp is one of the best options because:

- its inputs draw almost **no current**, and
- it always tries to make its **two inputs equal**.

### Op-amp regulator

| Part | Value / role |
|---|---|
| Non-inverting input (+) | **2.5 V** reference from a Zener |
| Inverting input (−) | Midpoint of a **10 kΩ / 10 kΩ** divider across the output |
| Output | Driven by the op-amp until the two inputs match |

The divider gives the (−) input half of the output. The op-amp settles when that half equals 2.5 V, i.e. when the output is **5 V**. If the output rises or falls, the midpoint becomes 2.6 V or 2.4 V, the op-amp sees the difference and corrects its output.

$$
V_{out} = V_{ref}\left(1 + \frac{R_{top}}{R_{bottom}}\right) = 2.5 \times \left(1 + \frac{10\ \text{k}}{10\ \text{k}}\right) = 5\ \text{V}
$$

You could also use a 5 V Zener and connect the output straight to (−). The divider version is more flexible: change the resistor ratio and you get **7 V, 10 V**, or any other output.

**Problem:** an op-amp can only supply a small current (milliamps, maybe a few hundred mA), not the 1, 2 or 3 A the follower circuit could deliver.

---

## 2. Op-amp + Follower: Accurate *and* High Current

Put the **follower transistor back** after the op-amp. The op-amp now drives the transistor's base, and the transistor supplies the load current.

**Where's the 0.7 V drop?** The follower still drops $V_{BE}$, but the feedback divider is taken from the **final output, after the transistor**. The op-amp measures the real output and raises its own output by whatever is needed (≈ 5.7 V) so the load still gets exactly 5 V. The $V_{BE}$ drop is **inside the loop**, so you can forget about it.

The output current is now limited by the **transistor**, not the op-amp: 1 A, 2 A or more, depending on the part.

**Capacitors** at the input and output aren't needed for the basic function (the circuit gives 5 V at 1 A without them). The designers add them to filter **noise** and smooth **ripple**. They're improvements, and you should know why they're there.

---

## 3. Simulation in the Video: Without vs. With Feedback

### Follower only (no feedback)

5.6 V Zener, 30 V input, output ≈ 5 V.

| Load | Load current | Output |
|---|---|---|
| 5 Ω | ≈ 1 A | ≈ 5 V |
| 2.5 Ω | ≈ 2 A | drops |
| 1 Ω | ≈ 2.5 A | far below 5 V |
| 5 Ω, input lowered to 6 V or 10 V | — | not regulated, well off 5 V |

### Op-amp + follower (with feedback)

Circuit Wizard has no 2.5 V Zener, so the instructor used **2.7 V**, giving an output of about **5.4 – 5.6 V** instead of 5 V. Divider 10 kΩ / 10 kΩ, 30 V input.

| Test | Result |
|---|---|
| Normal load | ≈ 1.85 A, output ≈ 5.5 V |
| 1 Ω load | ≈ 4.28 V (≈ 4.3 A); the follower alone had dropped to ≈ 2.5 V |
| 5 Ω load, input lowered to 6 V | **5.18 V** (the follower alone failed here) |
| 2.5 Ω load (≈ 2 A) | **4.94 V** |
| Input 10 V | still fine |

**Takeaway:** feedback gives much tighter regulation against both **load** changes and **input** changes. A common op-amp pattern: one input is a **reference**, the other comes from the **output**, and the op-amp keeps them equal.

---

## 4. Block: Current Limiter (Over-Current Protection)

**Problem:** say the supply is designed for **1 A**. If the user tries to draw 2 or 3 A, the circuit should refuse.

**Block:** a **small sense resistor** $R_{SC}$ in series with the output, and a small **transistor whose base–emitter junction sits across that resistor**. Its collector connects to the pass transistor's base.

$$
V_{R_{SC}} = I_{load}\,R_{SC}
\qquad
I_{limit} \approx \frac{V_{BE}}{R_{SC}} \approx \frac{0.6\text{ – }0.7\ \text{V}}{R_{SC}}
$$

- With **0.5 Ω**: 1 A gives 0.5 V, so the transistor is about to turn on; at 1.1 A it turns on.
- When it turns on, it **steals base current** from the pass transistor, which reduces the output current. The current stays at about **1 A** and can't go higher.

> **How to recognise it:** a transistor with a **small resistor between its base and emitter**, in the output path = **over-current protection**.

### Simulation in the video (0.7 Ω sense resistor → ≈ 1 A limit)

| Load | Current | Output voltage |
|---|---|---|
| 10 Ω | ≈ 0.5 A | 5 V |
| 5 Ω | ≈ 1 A | 5 V |
| 2.5 Ω | **still ≈ 1 A** | drops to ≈ **2.7 V** |

At 2.5 Ω the voltage drops because $1.1\ \text{A} \times 2.5\ \Omega \approx 2.7\ \text{V}$. **You can't limit the current and keep the voltage at the same time.** One of them has to give.

---

## 5. Block: Over-Voltage Protection

### SCR "crowbar"

An **SCR (thyristor)** across the output, from +V to ground. Its gate is driven through a **5.6 V Zener** and a **100 Ω** resistor.

- Normal output (5 V): the Zener doesn't conduct, and the SCR stays off.
- If the output rises above **5.6 V**, for example because the regulator failed: the Zener conducts, the SCR gate gets a pulse, and the SCR **shorts the output to ground**.

Why short your own output? Because there is a **fuse** in the supply. The short draws a large current, **the fuse blows**, and the circuit stops before the high voltage can damage the load. Then you replace the fuse and find the faulty part.

**Downside:** an SCR **latches**. After one pulse it stays on until the power is removed or the fuse blows.

*(My note: this circuit is commonly called a **crowbar** circuit. The fuse is **required**, otherwise the short just loads the supply.)*

### Non-latching version

Same idea (Zener + resistor sensing the voltage), but the Zener drives a **Darlington transistor (TIP122)** or a **MOSFET** instead of an SCR. When the voltage goes high it shorts the output. When the voltage comes back down it **releases** and the supply works normally again. The fuse may or may not blow during the short, depending on the circuit. The same block can protect **any input** against over-voltage.

---

## 6. Block: Foldback Current Limiting

**Problem with the plain limiter:** during a **short circuit**, it keeps the current at the full limit (e.g. 1 A) for as long as the short lasts. The output voltage is ≈ 0 V, so the whole supply voltage is across the pass transistor at full current. It gets very hot and may burn out.

**Improvement:** when the load draws too much or there's a short, the current should **drop by itself**. This is called **foldback**.

### The foldback curve (example from a datasheet)

- The output stays at **15 V** for any current up to **1 A**.
- Above 1 A, the voltage *and* the current fall together.
- At a dead short (0 V), the current settles at a lower value, **≈ 0.33 A** in this example.

### The block

Take the plain limiter (sense resistor $R_{SC}$ + transistor) and add a **voltage divider** ($R_1$, $R_2$) to the sense transistor's base. The current at the knee (max current) and the short-circuit current are both set by $R_{SC}$, $R_1$ and $R_2$.

> **How to recognise it:** one resistor + transistor = **plain current limiting**. Resistor + transistor + **a voltage divider on that transistor's base** = **foldback**.

*My note (standard formulas, for the common layout where the divider runs from the pass transistor's emitter to ground, the sense transistor's emitter is on the output, and $k = R_2/(R_1+R_2)$):*

$$
I_{knee} \approx \frac{V_{BE} + V_{out}(1 - k)}{k\,R_{SC}}
\qquad
I_{SC} \approx \frac{V_{BE}}{k\,R_{SC}}
$$

Since $V_{out}(1-k)$ disappears at a short, $I_{SC} < I_{knee}$. That's the "fold back".

### Simulation in the video

The instructor picked **0.5 Ω, 200 Ω and 3 kΩ** by trial, not by calculation.

| Test | Result |
|---|---|
| Normal, ≈ 5 Ω | ≈ 1 A, 5 V |
| 2.5 Ω | still 5 V, ≈ 2 A |
| 2 Ω | ≈ 5 V at **≈ 2.5 A** → this regulator gives 5 V / 2.5 A |
| **Short circuit** | 0 V, current falls to **≈ 1.7 A** instead of staying at 2.5 A |

Without foldback the transistor would keep carrying 2.5 A into the short and heat up a lot.

---

## 7. Putting It Together: Boosting a 7805

The **7805** is a 5 V regulator rated about **1 A**. To get 2 – 5 A, the usual trick (from the Practical Electronics Course) is to add an **external pass transistor**. The 7805 still sets the voltage, and the transistor supplies the extra current.

But that extra current is **unlimited**: a short could pull 10 A. So add:

1. A **current limiter** (sense resistor + transistor), or better, a **foldback** limiter (move the sense resistor and add a divider).
2. An **over-voltage** block (SCR + Zener crowbar, or another circuit).

The result is a solid regulator. These circuit shapes didn't come from nowhere. Engineers spent months or years refining them.

---

## 8. Block: Transistors in Parallel

All the high current flows through the **pass transistor**. If you need 10 A but only have 5 A transistors, you can put several **in parallel**, with care.

### BJTs: thermal runaway

Connecting BJTs directly in parallel (base to base, emitter to emitter) **burns them out**. No two transistors are identical: one turns on at 0.68 V, another at 0.72 V. So they don't share current equally.

BJTs have a **negative temperature coefficient** ($V_{BE}$ falls as they heat up), which leads to:

$$
\text{more current} \rightarrow \text{hotter} \rightarrow \text{even more current} \rightarrow \dots \rightarrow \text{burnt}
$$

This is **thermal runaway**. When the first transistor fails, its current moves to the others, which overload and fail one after another: a **chain failure**.

**Fix:** a **small resistor in each emitter** (emitter ballast). If one transistor carries more current, the voltage across its emitter resistor rises, which reduces its own $V_{BE}$ and pushes its current back down. It's a small **negative feedback** loop on each transistor, so they share the current **equally**.

Some companies use an **improved version**: a small sense resistor plus an extra transistor that takes some of the current when the voltage across that resistor rises. The goal is the same: **equal current sharing**.

### MOSFETs: easier

A MOSFET has a **positive temperature coefficient**: as it heats up, its on-resistance rises and the current through it **drops by itself**. Less current → less heat. It's self-balancing.

| Use | What's needed |
|---|---|
| **Switch** (fully on / fully off) | Connect gate–gate, source–source, drain–drain **directly**. No source resistors needed. Often a **gate resistor** (≈ 5 – 100 Ω, found by testing) on each gate, because each gate is a **capacitor** that draws a current spike when it switches. |
| **Linear / active region** (amplifiers, power supplies holding a voltage) | Direct parallel does **not** work. Add **source resistors**, just like BJT emitter resistors. |

---

## 9. Spotting the Blocks Inside Real Regulators

### LM317 (adjustable regulator)

The **functional block diagram** in its datasheet shows exactly what we built:

- an **op-amp**,
- followed by a **follower** made of transistors in a **Darlington-like** arrangement,
- a **Zener-type reference**,
- the **output fed back** to the op-amp,
- a resistor and **over-temperature / over-current protection** blocks.

The reference's other end goes to the **ADJ** pin. You connect it to ground through a resistor (a divider) to set the output voltage you want.

### 78xx family (7805, 7809, …)

The internal schematic looks large, but in blocks it's:

| Block | What it does |
|---|---|
| **Darlington** pass transistors | High output current with low drive current |
| Resistor before the output + transistor + divider network | **Current limiting with foldback** |
| **Divider** on the output | **Feedback** |
| Group of transistors | A **discrete op-amp** (the transistors that would be inside an op-amp IC) comparing the feedback with the reference |
| Resistor + **Zener**, follower, second divider | **Reference voltage** |

The exact values are the manufacturer's secret, but once you know the blocks, you can see what each part is for.

---

## Key Values

| Item | Value |
|---|---|
| Op-amp reference / divider | 2.5 V Zener, 10 kΩ / 10 kΩ → 5 V |
| Sim reference (no 2.5 V part) | 2.7 V → ≈ 5.4 – 5.6 V output |
| Follower only, 30 V in | ≈ 1 A at 5 Ω OK; sags at 2.5 Ω and 1 Ω; fails at 6 V in |
| With feedback | 5.18 V at 6 V in; 4.94 V at ≈ 2 A |
| Current limiter | $R_{SC}$ 0.5 Ω → ≈ 1 A; sim 0.7 Ω → 1 A, 2.5 Ω load → 2.7 V |
| Crowbar | 5.6 V Zener, 100 Ω, SCR, fuse |
| Non-latching OVP | Zener → TIP122 Darlington or MOSFET |
| Foldback example | 15 V up to 1 A, short-circuit current 0.33 A |
| Foldback sim | 0.5 Ω, 200 Ω, 3 kΩ: 5 V / 2.5 A normal, 1.7 A into a short |
| MOSFET gate resistors | ≈ 5 – 100 Ω |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll build the op-amp + follower regulator, add the current limiter and the foldback divider, and compare the short-circuit current with and without foldback.

---

## What I Learned

- **Feedback** = watch the output, correct the input. An op-amp makes it easy: reference on one input, output (through a divider) on the other, and it keeps them equal.
- Put the follower **inside the loop** (feedback after the transistor) and the 0.7 V drop disappears from the output. The op-amp sets the accuracy, the transistor supplies the current.
- **Current limiter block:** small resistor across a transistor's base–emitter. $I_{limit} \approx 0.7\ \text{V} / R_{SC}$. Limiting current always costs voltage.
- **Foldback** = current limiter + divider on the sense transistor's base. It reduces the current during a short, so the pass transistor doesn't cook.
- **Crowbar:** Zener + SCR shorts the output on over-voltage and blows the fuse. It latches. A transistor or MOSFET version releases on its own.
- **BJTs in parallel need emitter resistors** (negative temperature coefficient → thermal runaway). **MOSFETs** share current by themselves when used as **switches**, but need source resistors in **linear** use.
- The **LM317** and **7805** are made from the same blocks: reference, op-amp, Darlington follower, feedback divider, current limit / foldback, thermal protection.

### Next to try

- Build the op-amp + follower regulator in Circuit Wizard (2.7 V Zener → 5.4 V, or change the divider to get exactly 5 V with 2.7 V) and repeat the load and input tests.
- Work out the divider for exactly 5.0 V from a 2.7 V reference: $R_{top}/R_{bottom} = 5/2.7 - 1 \approx 0.85$.
- Add the 0.5 Ω / 0.7 Ω current limiter, then the foldback divider, and compare the **pass-transistor power** during a short: $P \approx V_{in} \times I_{SC}$.
- Check my foldback formulas against the 0.5 Ω / 200 Ω / 3 kΩ values in the video.
- Simulate the SCR crowbar with a fuse by forcing the output above 5.6 V.
- Try two BJTs in parallel with and without emitter resistors and compare how the current splits.
