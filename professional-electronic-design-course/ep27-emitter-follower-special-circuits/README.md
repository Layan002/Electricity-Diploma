# Ep27 – The Emitter Follower and Its Special Circuits

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 27- الترانزستور التابع ودوائره الخاصة (The emitter follower and its special circuits)](https://www.youtube.com/watch?v=_LC7JSvfpYY) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 16 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

Level 3 is about **special "block" circuits**: small, standard building blocks that show up again and again inside bigger circuits and ICs. The goal is to **recognise each block on sight** and know what it is for. From now on the course relies on a general understanding of **transistors, voltage dividers and diodes / Zeners**.

This episode starts with the simplest and most common block, the **emitter follower** (also called a **common-collector** stage or **voltage follower**), and grows it step by step into a real **5 V linear voltage regulator** that can supply over **1 A**:

1. What an emitter follower does and why it's useful.
2. The problem: a **voltage divider collapses under load**.
3. **Divider + follower**: the load no longer drags the voltage down.
4. The next problem: the output still **follows changes in the input** → replace the bottom resistor with a **Zener**.
5. Hardware test with a **2N2222**: fine for small loads, fails with a **0.5 A lamp**.
6. A **power transistor** (2N3055) handles the current but has a **low β**, so it loads the Zener circuit.
7. The fix: a **Darlington pair**, then a **complementary Darlington** (Sziklai pair) with a smaller voltage drop.
8. A look at the **push-pull** block for positive and negative outputs.

---

## 1. The Emitter Follower

An NPN transistor with the input on the **base** and the output taken from the **emitter**. The output **follows** the input: whatever change happens at the input is copied to the output, with **no voltage gain** (no amplification and no reduction), apart from the base–emitter drop:

$$
V_{out} = V_E \approx V_B - V_{BE} \approx V_B - 0.7\ \text{V}
$$

Two bias resistors on the base keep the transistor in the **active region**, away from both cut-off and saturation. (The follower can also be built from an op-amp IC instead of a transistor.)

### Why use it?

| Property | What it means |
|---|---|
| **Very high input resistance** | It draws almost **no current** from the source. A weak signal (one that can't supply voltage *and* current to the next circuit) can feed it "without effort". |
| **Current boost** | The output is a copy of the input, but backed by a **much larger current** from the supply. It doesn't make the signal bigger (a ±1 V signal stays ±1 V, it does **not** become ±5 V); it makes it able to **drive a load**. |
| **Isolation** | It separates the weak input from the load, so the load can't disturb the input. |

*My note (standard formula):* the base only has to supply the emitter current divided by the current gain:

$$
I_B = \frac{I_E}{\beta + 1}
$$

That's why the input sees such a high resistance, roughly $(\beta+1)\,R_{load}$.

---

## 2. The Problem: a Voltage Divider Under Load

A simple divider to get 5 V from 9 V (e.g. to charge a phone):

| Part | Value |
|---|---|
| Supply | 9 V |
| $R_1$ (top) | 1 kΩ |
| $R_2$ (bottom) | 1.25 kΩ |

$$
V_{out} = V_{in}\,\frac{R_2}{R_1 + R_2} = 9 \times \frac{1.25}{1 + 1.25} = 5\ \text{V}
$$

With no load the output is exactly **5 V**. But add a **200 Ω load** and it collapses to about **1.3 V**. The 200 Ω is in parallel with $R_2$, which lowers the bottom resistance and pulls the midpoint down.

*My check:*

$$
R_2 \parallel R_L = \frac{1250 \times 200}{1250 + 200} \approx 172\ \Omega
\quad\Rightarrow\quad
V_{out} = 9 \times \frac{172}{1000 + 172} \approx 1.32\ \text{V}
$$

The divider simply **can't give the load its current and keep its voltage**.

---

## 3. Divider + Emitter Follower

Put a follower between the divider and the load. The load current now comes from the **transistor**, not from the divider; the divider only supplies the tiny base current.

- With the original 1.25 kΩ, the output is **below 5 V** because the follower loses **0.6 – 0.7 V** ($V_{BE}$).
- **Compensate** by raising $R_2$: with **1.8 kΩ** the base sits at about **5.7 V**, so the emitter gives about **5 V** (with 1.5 kΩ it's a little lower).

*My check:* $9 \times \dfrac{1.8}{1 + 1.8} \approx 5.79\ \text{V}$, minus 0.7 V ≈ **5.1 V**.

### Simulation results (from the video)

| Load | Output | Load current |
|---|---|---|
| none | ≈ 5 V | — |
| 200 Ω | ≈ 5 V | ≈ 24 mA |
| smaller load (more current) | drops only slightly below 5 V | higher |

The follower **relieves the divider** and **satisfies the load**. It has limits, but it handled ≈ 50 mA easily.

### The remaining weakness

Suppose the 9 V comes from a **solar cell** that rises and falls. If it suddenly jumps to **20 V**, the divider midpoint rises to about **12 – 13 V** and the output becomes about **11.6 – 11.9 V**. That would **burn the phone**.

A divider gives a **fixed fraction** of the input. It doesn't hold a fixed voltage: when the input changes, the output changes with it.

---

## 4. Design Task: a 5 V Regulator

> Use the follower to build a **5 V regulator** that stays at 5 V while the input changes from **9 V to 30 V**, with a load of about **200 Ω**.

This is the basic circuit **inside the voltage regulators you buy**.

### The solution: a Zener on the base

The voltage across $R_2$ is what reaches the base, so **that** voltage has to stay fixed. Which component holds a voltage constant? A **Zener diode**.

- We want 5 V out, and the follower loses ≈ 0.7 V, so the Zener should be about **5.6 V** (a standard value):

$$
V_{out} \approx V_Z - V_{BE} = 5.6 - 0.7 \approx 4.9\ \text{V}
$$

- **Input at 9 V, 20 V or 30 V → output stays ≈ 5 V.** The resistor's job was only to *share* the voltage; the Zener's job is to *hold* it.
- The Zener circuit only supplies about **98 µA** to the base, while the load gets **many times more**. A 100 Ω load (twice the current) still gives ≈ 5 V.

**Follower + Zener** is a very well-known pair: a **fixed** lower voltage, **backed by current**, without tiring the circuit before it.

*My note (standard design formula):* the series resistor must pass the Zener's minimum current **plus** the base current at the **lowest** input voltage:

$$
R_S \le \frac{V_{in,min} - V_Z}{I_{Z,min} + I_{B,max}}
$$

---

## 5. Hardware Test 1: 2N2222 Follower

| Part | Value |
|---|---|
| Input | variable (9 – 30 V); tested at 11.4 V |
| Series resistor | 0.5 kΩ |
| Zener | 5.6 V |
| Follower | 2N2222 (NPN) |

| Load | Result |
|---|---|
| none | **5.27 V** |
| LED + resistor (≈ 30 mA) | still ≈ **5 V** |
| Lamp (≈ 0.5 A) | voltage **collapses**, lamp doesn't light properly |

The lamp has a very low resistance and tries to pull a large current. A small transistor like the 2N2222 can only pass about **100 – 200 mA**, so one of two things happens: the **voltage collapses** or the **transistor burns**.

---

## 6. Power Transistors and the β Problem

You could replace the circuit with a ready-made regulator IC, or use a **bigger transistor**. But high-current transistors usually have a **low β**:

| Transistor | Type | DC current gain ($h_{FE}$) | Current |
|---|---|---|---|
| BC547 | small NPN | ≈ 200 | small (≈ 100 mA) |
| 2N2222 | small NPN | 100 – 300 | ≈ 100 – 200 mA (per the video) |
| 2N3055 | power NPN | **20 – 70** | ≈ 4 A (per the video) |

The 2N3055's gain is **5 to 7 times lower**. A low β means the base needs a **bigger current** for the same load current, and that current comes from the **Zener circuit**, which gets **overloaded**.

### Simulation (from the video), 10 Ω load ≈ 0.5 A

| Follower | Base current from the Zener circuit | Problem |
|---|---|---|
| 2N2222 (earlier, light load) | ≈ 100 µA | — |
| 2N2222, 0.5 A | ≈ 4 mA | overheats / fails at 0.5 A |
| 2N3055, 0.5 A | ≈ 5 mA | handles the current, but loads the Zener circuit |

*My note:* with 11.4 V in and 0.5 kΩ, the series resistor only supplies about $(11.4 - 5.6)/500 \approx 11.6\ \text{mA}$ in total. If the base takes 5 – 8 mA, very little is left for the Zener, and at a lower input it would drop out of regulation.

So: a small transistor **burns**, a big one **loads the stage before it**.

---

## 7. The Darlington Pair

Use **two followers in a row**: a small, high-β transistor drives the **base** of the power transistor.

- The small transistor draws only about **28 µA** (practically zero) from the Zener circuit.
- The power transistor passes about **0.5 A** to the load.
- Now there are **two $V_{BE}$ drops** (≈ 0.7 + 0.7 ≈ 1.4 V), so the Zener was raised from 5.6 V to **6.8 V**:

$$
V_{out} \approx V_Z - 2V_{BE} = 6.8 - 1.4 \approx 5.4\ \text{V}
$$

Simulated output ≈ **5.4 – 5.5 V**, better than before, with a very small base current.

*My note (standard formula):* the overall current gain is roughly the **product** of the two:

$$
\beta_{total} \approx \beta_1 \beta_2 \qquad \text{e.g. } 200 \times 50 = 10\,000
$$

Use a Darlington when you need a **high-current transistor** but its **β is small**, or when you want a huge gain (tens of thousands or more). It's very common inside regulator circuits.

---

## 8. The Complementary Darlington (Sziklai Pair)

A second form that uses a **PNP power transistor** with an NPN driver:

- The small **NPN** (2N2222) is still the follower whose base sits on the Zener.
- Its collector drives the base of a **PNP power transistor** (**MJ2955 / 2N2955**, the complementary partner of the 2N3055).
- The PNP's emitter is on the input, and its collector feeds the output.

Only **one 0.7 V** is lost between the Zener and the output, so the **5.6 V Zener** can be kept:

$$
V_{out} \approx V_Z - V_{BE} = 5.6 - 0.7 \approx 5\ \text{V}
$$

### Advantages over the standard Darlington

| | Darlington | Complementary Darlington |
|---|---|---|
| Voltage lost | 2 × 0.7 ≈ 1.4 V | 1 × 0.7 V |
| Zener needed for 5 V | 6.8 V | 5.6 V |
| Current gain | ≈ β₁β₂ | ≈ β₁β₂ |

Less **voltage drop** is needed from the input, and the β is still multiplied. In the simulation the output held **≈ 5 V** at **0.5 A** and also at **1 A**.

---

## 9. Hardware Test 2: 2N3055 Power Transistor

The 2N3055 comes in a metal **TO-3** package, made to sit on a **heat sink**.

- It has only **two pins**: **base** and **emitter**. The **case is the collector**.
- Hold it with the pins nearer one side, and you can tell which is which by their positions relative to the mounting holes.
- Mount it with **screws and nuts**, often with **thermal paste**, solder a wire to each pin, and take the **collector wire from a mounting screw**.
- The nuts should be **metal**: plastic ones (used temporarily in the video) would melt as the transistor heats up.

| Load | Output | Notes |
|---|---|---|
| none | 5.3 V | |
| LED | ≈ 5 V | fine |
| Lamp (≈ 1 A) | dropped ≈ 0.5 V (≈ 4.5 – 4.8 V) | lamp fully on, transistor only slightly warm |
| **Base current** | **≈ 7.8 mA** | **far too high**, because of the low β |

It works, but 7.8 mA from the Zener circuit would overload it. We want the base current in **microamps**.

---

## 10. Hardware Test 3: Complementary Darlington

Changes from the previous build: the 2N2222 goes back in as the driver, an **MJ2955** PNP power transistor is added, and the **5.6 V Zener** is kept.

| Measurement | Value |
|---|---|
| Lamp current | ≈ **1.1 A** |
| Current drawn from the Zener circuit | ≈ **38 µA** (was 7.8 mA) |
| Output at the lamp | ≈ **4.9 V** (≈ 5 V) |

A **200×** reduction in the current taken from the Zener stage, with the same output. This is an excellent **high-current, well-regulated 5 V** circuit.

The step-by-step improvement here is exactly how real designs evolve: you don't see at first why a designer used *this* transistor or *two* transistors, until you realise it was to cover more current or avoid two 0.7 V drops.

---

## 11. Push-Pull Followers

What if the output must go both **positive and negative**, not just between 0 V and +5 V? Use the **push-pull** stage:

- The same follower idea, but with an **NPN** (for positive outputs) and a **PNP** (for negative outputs) sharing the input and the output.
- Whatever goes in comes out: positive stays positive, negative stays negative, now with current behind it.
- In bigger circuits each side is often a **Darlington**, giving a **Darlington push-pull block**.

Learn these as **blocks**, so you can spot them by eye in large schematics and know what each one is for.

---

## Summary of Values from the Video

| Circuit | Item | Value |
|---|---|---|
| Divider only | Supply / $R_1$ / $R_2$ | 9 V / 1 kΩ / 1.25 kΩ |
| Divider only | Output, no load / 200 Ω load | 5 V / ≈ 1.3 V |
| Divider + follower | $R_2$ for ≈ 5 V out | 1.8 kΩ (base ≈ 5.7 V) |
| Divider + follower | Output / current, 200 Ω load | ≈ 5 V / ≈ 24 mA |
| Divider + follower | Input jumps to 20 V | output ≈ 11.6 – 11.9 V ✗ |
| Design task | Input / output / load | 9 – 30 V / 5 V / ≈ 200 Ω |
| Zener + follower | Zener | 5.6 V → ≈ 4.9 V out at 9, 20, 30 V |
| Zener + follower | Base current | ≈ 98 µA |
| Hardware, 2N2222 | Series R / input | 0.5 kΩ / 11.4 V |
| Hardware, 2N2222 | Output: no load / LED ≈ 30 mA / lamp 0.5 A | 5.27 V / ≈ 5 V / collapses |
| β values | BC547 / 2N2222 / 2N3055 | ≈ 200 / 100 – 300 / 20 – 70 |
| Sim, 10 Ω load (≈ 0.5 A) | Base current 2N2222 / 2N3055 | ≈ 4 mA / ≈ 5 mA |
| Sim, Darlington | Zener / driver base current / output | 6.8 V / ≈ 28 µA / ≈ 5.4 V |
| Sim, complementary Darlington | Zener / output | 5.6 V / ≈ 5 V at 0.5 A and 1 A |
| Hardware, 2N3055 | Output: no load / lamp ≈ 1 A | 5.3 V / ≈ 0.5 V lower |
| Hardware, 2N3055 | Base current | ≈ 7.8 mA |
| Hardware, 2N2222 + MJ2955 | Lamp current / base current / output | ≈ 1.1 A / ≈ 38 µA / ≈ 4.9 V |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add the divider vs. divider + follower comparison, the Zener regulator over a 9 – 30 V input sweep, and the Darlington and complementary Darlington versions with a 0.5 – 1 A load.

---

## What I Learned

- The **emitter follower** copies its input to the output (minus ≈ 0.7 V) with **no voltage gain**, but with a **very high input resistance** and a **large output current**. It's a **buffer**.
- A **voltage divider can't drive a load**: the load sits in parallel with the bottom resistor and drags the voltage down (5 V → 1.3 V with 200 Ω).
- A divider gives a **fraction** of the input, so its output **follows input changes**. A **Zener** on the follower's base fixes the voltage instead: **Zener + follower = simple linear regulator**.
- **Compensate for $V_{BE}$** when choosing the Zener: 5.6 V for one drop, 6.8 V for two.
- Power transistors carry more current but have a **low β**, so they pull too much current from the stage before them.
- A **Darlington** multiplies the β (base current in µA) but costs **2 × 0.7 V**. A **complementary Darlington** (NPN driver + PNP power) also multiplies β but costs only **one 0.7 V**.
- The **2N3055** (TO-3) uses its **case as the collector** and needs a heat sink, metal hardware and often thermal paste.
- **Push-pull** followers (NPN + PNP) handle both positive and negative outputs.

### Next to try

- Simulate the divider with and without the follower at 200 Ω, and check my calculated 1.32 V.
- Sweep the input from 9 V to 30 V on the Zener regulator and plot the output.
- Size the Zener series resistor for the **worst case** (9 V input, full load) with the formula in section 4, and see what happens to regulation if it's too big.
- Compare the 2N3055 alone, the Darlington, and the complementary Darlington at 0.5 A and 1 A: output voltage, base current, and the minimum input before the output drops.
- Work out the **heat in the pass transistor**, $P = (V_{in} - V_{out}) \times I_{load}$. At 30 V in and 1 A that's about 25 W, which explains the heat sink.
