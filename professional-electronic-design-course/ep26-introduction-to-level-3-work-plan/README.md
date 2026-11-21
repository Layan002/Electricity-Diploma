# Ep26 – Introduction to Level 3 and Its Work Plan

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 26- مقدمة للمستوى الثالث وخطة العمل له (Introduction to Level 3 and its work plan)](https://www.youtube.com/watch?v=nM9hcel5qT0&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=164) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 21 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

This is a short (≈ 9 min) **introduction episode** with no circuit to design. It explains what Level 3 is about, why it is called **"Special Block Circuits"** (الدوائر الخاصة الصندوقية), how the instructor groups electronic circuits in general, and what the student is expected to do during this level.

1. What a "block" circuit is and why we study circuits this way.
2. Making peace with yourself: big, crowded schematics are not something to fear.
3. A general way to group circuits: **decision-making (control)** vs. **signal processing**.
4. The task for this level: record each block, its shape, and its function.

---

## 1. Why "Block" Circuits?

Real products (amplifiers, voltage regulators, ICs) contain schematics full of transistors packed together. Looked at as one piece, they seem impossible to follow. The idea of Level 3 is to treat each recognisable part of such a schematic as a **black box**:

- You learn the **shape** of a block once.
- You learn its **function** (it regulates voltage, it regulates current, it protects against a certain fault, …).
- After that you don't need to re-analyse what happens inside it every time. You recognise it on sight and use it.

So a huge circuit becomes a set of boxes, each with one job. Once you know the boxes, you can usually understand **≈ 80 – 90 %** of what a large circuit does and why each part is there.

### What this gives you as a designer

- You can tell **where to go** in a circuit if you want to change its behaviour.
- You can predict what happens to the **whole circuit** if you change one part (for example one resistor), because you know which block that part belongs to and what the block does.
- You don't need to justify **every exact value** in a commercial schematic. Many values were tuned by the manufacturer to reach their final product.

```
 ┌─────────┐    ┌───────────┐    ┌───────────┐    ┌─────────┐
 │ Input   │ →  │ Block A   │ →  │ Block B   │ →  │ Output  │
 │         │    │ (regulate)│    │ (protect) │    │         │
 └─────────┘    └───────────┘    └───────────┘    └─────────┘
   A big schematic read as boxes, each with one known function.
```

---

## 2. Making Peace With Yourself

After finishing Levels 1 and 2 (simple and advanced design), the next step is harder circuits. The instructor's message here is mainly about **mindset**:

- Even many experienced engineers don't fully understand these internal schematics, so feeling lost at first is normal.
- The goal is not perfect understanding of every component, but enough understanding to **analyse, predict, and modify** the circuit with confidence.
- Stay **relaxed** when you meet a large circuit. Split it into blocks instead of trying to swallow it whole.

---

## 3. Two Families of Circuits

The instructor groups electronic circuits into two families. He points out this is **his own classification**, not one from textbooks, and that the two families overlap in many real circuits.

| Family | What it does | Output | Examples |
|---|---|---|---|
| **Decision-making / control** (صنع القرار) | Decides whether something works or not | Only two states: ON / OFF, high / low, 1 / 0 (e.g. 5 V / 0 V in digital logic) | Charge or don't charge, discharge or don't, motor left or right, remote control of a door or a toy car, LED or relay on / off, digital logic |
| **Signal processing** (معالجة الإشارة) | Changes or improves a signal | A continuous range of values | Filters (remove noise), amplification, modulation (AM, FM), signal generation, voltage and current regulation |

- The **communications** world is almost entirely **signal processing**.
- The **control** world is almost entirely **decision-making**.
- **Voltage / current regulation** fits signal processing (e.g. bringing 10 V down to a clean, noise-free 5 V), although some people class it as control, and they have their reasons.

### Many circuits need both

A **remote control** is a good example:

1. The transmitter uses a **modulation** circuit (signal processing) to send the signal over a distance.
2. The receiver then makes a **decision**: which output turns ON and which stays OFF (control).

### Where we've been, and where we're going

Most circuits in **Levels 1 and 2** were decision-making circuits: an LED on or off, a relay on or off, a motor this way or that way. Level 3 moves between **both families** and covers many different topics and blocks.

---

## 4. The Work Plan for Level 3

The student's job during this level:

1. **Record every circuit shape** shown in the episodes (in a notebook or your own notes).
2. Next to each shape, write its **function in one sentence**, in your own words. For example: *"This block looks like this, and it does X."*
3. **Don't try to prove the function mathematically.** Many of these blocks were the subject of master's and PhD work. Take what others have proven and use it. The instructor will show the function, sometimes in theory and sometimes only in simulation, not always in hardware.
4. **Be flexible and understand, don't memorise.** Like in Levels 1 and 2 (a small piece of information, then a task to apply it), the aim is to adapt each block to new problems.

*My note:* for this repo, I'll add a short **"Block card"** section to each Level 3 episode README with the block's schematic, its one-sentence function, and where it appears. This matches the instructor's plan and will build a reference library as the level goes on.

---

## Key Points

| Item | Value / idea |
|---|---|
| Level 3 name | Special "Block" Circuits (الدوائر الخاصة الصندوقية) |
| Approach | Treat each sub-circuit as a black box: shape + function |
| Target understanding | ≈ 80 – 90 % of a large circuit's purpose |
| Circuit families | Decision-making (ON / OFF) and signal processing |
| Student task | Shape + one-sentence function for every block, no mathematical proofs |

---

## My Circuit Wizard Simulation

*None needed.* This is an introduction episode with no circuit.

---

## What I Learned

- A big schematic is just a set of **known blocks**. Recognising them is what lets me read, change, and predict a circuit.
- Circuits can be grouped as **decision-making** (two states) or **signal processing** (continuous values), and most real products mix both.
- Levels 1 and 2 were mostly decision-making; Level 3 widens that to signal processing too.
- I don't need to derive every block mathematically. Knowing its **shape, function and how to use it** is the goal.

### Next to do

- Keep a running list of Level 3 blocks (shape + one-sentence function). Ep27–29 already give the first ones: emitter follower, Zener + follower regulator, Darlington / complementary Darlington, push-pull, current limiter, crowbar, foldback, diode-connected transistor, $V_{BE}$ multiplier.
