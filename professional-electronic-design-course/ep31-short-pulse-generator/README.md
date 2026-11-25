# Ep31 – Short Pulse Generator: Why?

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 31- مولد نبضة قصيرة .. لماذا ؟ (Short pulse generator: why?)](https://www.youtube.com/watch?v=IgWsELIFlts&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=169) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 26 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

A very simple block, but an important one: the **single-pulse generator**, built from just a **resistor and a capacitor**. The episode shows how one small addition turns a beginner's circuit that *sometimes* works into a professional circuit that *always* works.

1. The RC circuit two ways: capacitor at the bottom (charging curve) vs. capacitor in series (a single pulse).
2. Pulse direction: resistor to ground vs. resistor to the supply.
3. Driving a transistor with the pulse: inverting and **stretching** the pulse.
4. Analysing a two-transistor pulse circuit (capacitor voltage jumps to −4.4 V).
5. Adding a **holding transistor** so a very short trigger still gives a full output pulse.
6. **Design task:** fix a CD4017 counter that starts on a random LED, using a **power-on reset**.

Prerequisite: a good feel for how a capacitor behaves (charging, and the "flip-flop" capacitor from [ep22](../ep22-flip-flop-capacitor-oscillator-status-indicator/)).

---

## 1. The RC Circuit, Two Ways

### 1.1 Resistor on top, capacitor to ground (output across C)

Close the switch:

- The empty capacitor draws a **large current** at first, which falls to ≈ 0 as it fills up.
- The voltage across C **rises gradually** and settles at the supply voltage.

This is the familiar **charging curve**.

### 1.2 Capacitor on top, resistor to ground (output across R)

Swap the two parts and measure at the same point:

- Before switching, there's no current, so 0 V across R.
- At switch-on the empty capacitor "accepts" a big rush of current. That current flows through R, so the voltage across R **jumps high immediately**.
- As C charges, the current falls, so the voltage across R **decays back to 0**.

The result is **one single pulse** (a spike), not a repeating pulse train. Its width is set by the time constant:

$$
\tau = R \cdot C
$$

The capacitor "stands in front of the cannon": it takes the push of electrons and passes a short burst to the next stage. This block is called a **pulse generator** (مولد نبضة واحدة).

### 1.3 Resistor to the supply instead of ground

If R goes to **+V** instead of ground:

- The output sits **high** normally.
- When the input steps **down** (a negative-going edge), the capacitor's charge drags the output **sharply down**. Then it recharges through R and returns high.

So the pulse direction depends on which rail the resistor goes to and which way the input edge moves.

---

## 2. Driving a Transistor with the Pulse

A transistor (or MOSFET) is controlled by its input voltage, so feed it the pulse:

- The pulse's sharp rise **switches the transistor ON**, and it pulls its output (collector/drain) to **0 V**.
- The output stays low until the pulse decays below the turn-on voltage, then goes back high.

The output is an **inverted pulse**. Flipping the RC arrangement flips the output pulse direction too, and it comes out **wider**. This version is sometimes called a **pulse extender (pulse stretcher)**: it makes the pulse longer so it has more effect on the next stage.

With a **BJT** (NPN or PNP) instead of a MOSFET, add a **base resistor** so the base can't draw more current than it can handle. The pulse timing then depends on both resistors, not just one.

---

## 3. Analysing a Two-Transistor Pulse Circuit

<!-- Add figure: media/ep31-two-transistor-pulse-generator.png -->

**Setup (5 V supply):** input → Q1. Q1's collector connects through a capacitor to Q2's base. Q2's base also has a resistor to +5 V. Q2's collector is the output.

**Resting state (input = 0):**

- Q1 is **off**, so its collector sits at **5 V**.
- Q2's base gets current from its resistor, so Q2 is **on**. Its base is clamped at ≈ **0.6 V** and the output is **≈ 0 V**.
- The capacitor has 5 V on one side and 0.6 V on the other, so it holds **4.4 V** (positive on the Q1 side).

**A pulse arrives:**

1. The rising edge saturates Q1, and its collector drops from **5 V to 0 V**.
2. The capacitor can't change its voltage instantly. Its other side must stay 4.4 V below, so Q2's base jumps to **−4.4 V**.
3. Q2 can't conduct with a negative base, so it turns **off** and the output jumps **high** (to 5 V).
4. The resistor slowly charges Q2's base back up: −4.4 → −3 → … → 0 → 0.6 V.
5. At ≈ 0.6 V, Q2 turns **on** again and the output returns to **0 V**.

**Analysis tip:** with a capacitor in a DC circuit, look at the voltage on **each side**, work out its charge and polarity, then remember that **a jump on one side carries the other side with it**.

This is the same idea as the flip-flop capacitor in ep22, but here it gives one pulse instead of oscillating.

### The weakness, and the fix

In this circuit the **input has to stay on** for the whole output pulse. If the input pulse is very short, the output drops back before the capacitor has recharged.

**Fix: a holding (latching) transistor.** Take the output pulse and use it to switch on a second transistor in parallel with Q1, so it also holds Q1's collector at ground (the latch idea from Level 2). Now:

- Even a **very short** input pulse sends Q2's base to −4.4 V.
- The output holds Q1's collector low by itself until the capacitor recharges.
- The output pulse **always has its full length**, set by R and C, whatever the trigger length.

### Why this is useful

Some circuits are too **slow to notice a very short pulse** (for example, a stage that has to count pulses or react to them). This block **stretches** a short pulse into a longer one that the next circuit can reliably detect.

---

## 4. Design Task: Fixing a CD4017 Counter

**The beginner's circuit (from the Digital Electronics course):**

- **CD4017** decade counter, 5 V.
- Outputs **Q0, Q1, Q2** drive three LEDs. **Q3 → RESET**, so the count goes Q0 → Q1 → Q2 → back to Q0.
- **Clock** comes from a push button with an RC network (against noise and **switch bounce**). Each press gives a rising edge that moves the lit LED along.
- Intended: at power-up, **LED 1 (Q0)** lights.

**The problem:** in simulation it works, but on real hardware, each time the supply is connected it lights **LED 1, LED 2, LED 3 or nothing at all**, randomly. It works, but it isn't professional.

### The fix: power-on reset pulse

RESET is what forces Q0 on. So make the IC **reset itself at power-up**, using today's block:

<!-- Add figure: media/ep31-cd4017-power-on-reset.png -->

| Part | Value | Connection |
|---|---|---|
| Capacitor | **100 nF** | between **+5 V** and **RESET** |
| Resistor | **10 kΩ** | between **RESET** and **ground** |

When power is applied, +5 V jumps from 0 to 5 V. The capacitor passes that jump as a **short high pulse** on RESET, which then decays to 0 through the 10 kΩ. The IC resets to **Q0** and then counts normally.

$$
\tau = 10\ \text{k}\Omega \times 100\ \text{nF} = 1\ \text{ms}
$$

**Choosing values:**

- A **smaller capacitor** gives a faster, shorter pulse. If the IC doesn't see it (no reset), **increase C**.
- 100 nF is the common practical choice.
- A **large resistor** (e.g. 10 kΩ) so it wastes almost no current. It may work without the resistor (the IC's input has its own path to ground), but it's better to include it.
- The values are tuned by trying them and watching how the circuit behaves.

### Result (hardware)

| Test | Before | After |
|---|---|---|
| Power applied repeatedly (≈ 5 s off between tries) | LED 1, 2, 3 or none, randomly | **LED 1 every time** |
| Fast on/off switching | random | **LED 1 every time** |
| Clock presses | 1 → 2 → 3 → 1 | 1 → 2 → 3 → 1 (unchanged) |

One resistor and one capacitor turned an unreliable design into a professional one.

---

## 5. Other Uses

- **Starting kick:** a load that won't start at 5 V can be given a **short pulse of a higher voltage** to get it going, and then it runs on 5 V normally.
- Any place you need **one short pulse** from a voltage step: power-on reset, edge detection, triggering the next stage.

---

## What I Learned

- **C in series + R to a rail = single pulse generator.** The output is a spike of width ≈ $RC$ on each input edge.
- Which rail the resistor goes to, and which way the edge moves, decide the pulse direction.
- A transistor after the RC can **invert** and **stretch** the pulse. Add a **holding transistor** so even very short triggers give a full-length pulse.
- In capacitor circuits, track the voltage on both sides: a jump on one side moves the other side by the same amount (e.g. Q2's base going to −4.4 V).
- **Power-on reset** (100 nF from +V to RESET, 10 kΩ to ground) makes a CD4017 always start on Q0. Small additions make a design professional.

---

## Files

- `media/` – Circuit Wizard simulations and figures (to be added)
