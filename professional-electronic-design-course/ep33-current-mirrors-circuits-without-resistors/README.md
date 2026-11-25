# Ep33 – Current Mirrors and Circuits Without Resistors

**Course:** Professional Electronic Design Course – Level 3 (Special "Block" Circuits)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 33- تصميم مرايا التيار (Current Mirror) ودوائر من غير مقاومات (Designing current mirrors and circuits without resistors)](https://www.youtube.com/watch?v=Z-nOaz-q6ZI&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=171) (video in Arabic)
**Tool:** Circuit Wizard
**Watched:** 26 Nov 2026

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## 1. Why we need something new

In the previous episode ([ep32](../ep32-constant-current-source/)) we built a **constant current source/sink**: hold a fixed voltage across a fixed resistor, and you get a fixed current through the load.

The weak point of that circuit is the **resistor in the current path**:

- Its value can drift (temperature, tolerance).
- For high currents it wastes power ($P = I^2R$).
- Inside an IC, resistors take up **much more silicon area** than transistors.

The answer engineers came up with is a new building block: the **current mirror**.

---

## 2. The idea of a current mirror

A current mirror takes the current flowing in one branch (the **input / reference**) and **copies** it into another branch (the **output**), like a mirror.

### How it works

<!-- Add figure: media/ep33-basic-current-mirror.png -->

- Two **identical** transistors Q1 and Q2 have their **bases tied together** (and both emitters to ground for NPN).
- Q1 is **diode-connected**: its collector is shorted to its base. The input current flows into this collector.
- The input current sets the base–emitter voltage $V_{BE}$ of Q1.
- That same $V_{BE}$ is applied to Q2. Since Q2 is identical, the same $V_{BE}$ produces the **same collector current**.

$$
I_{out} \approx I_{ref}
$$

The load sits in Q2's collector, **with no resistor in its path**.

The same idea works with **NPN, PNP and MOSFETs**. The drawing is the same; only the device equations differ.

### Setting the reference current

With a resistor $R$ from $V_{CC}$ to the diode-connected transistor:

$$
I_{ref} = \frac{V_{CC} - V_{BE}}{R}
$$

---

## 3. Assumptions behind the basic mirror

1. **Perfect matching.** The two transistors must be *matched*, not just "the same part number". Two discrete 2N2222s are similar but not matched.
2. **High current gain (β).** Base currents are small compared to collector currents, so they can be neglected. With finite β the output is slightly lower:

$$
I_{out} = \frac{I_{ref}}{1 + \dfrac{2}{\beta}}
$$

Because matching is so hard with separate parts, real current mirrors are built **inside ICs**, where the transistors sit next to each other on the same die.

---

## 4. Multiple outputs and current ratios

One reference transistor can drive **several output transistors**, giving several copies of the current (for example, several LED strings each getting the same current).

You can also **scale** the current by using different numbers (or areas) of transistors on each side:

$$
I_{out} = \frac{N_{out}}{N_{in}} \, I_{ref}
$$

Example: 4 transistors in parallel on the input side and 3 on the output side give $I_{out} = \tfrac{3}{4} I_{ref}$. Equal numbers give $I_{out} = I_{ref}$.

---

## 5. Current mirrors inside real ICs

- On an IC layout picture, current mirrors show up as small matched transistor pairs, while a single resistor takes **far more area**.
- Both a resistor and a current mirror can **set a current**, so IC designers often use mirrors **instead of resistors** to save area.
- **REF200 example:** this IC contains **two 100 µA current sources** plus a **current mirror** (pins: mirror input, mirror output, common). TI builds it as a ready-made chip because you can't get that matching with external transistors. You add an external resistor, and $V = I \cdot R$ gives you an accurate reference voltage.

---

## 6. Improving the mirror

### 6.1 Emitter degeneration resistors

Small, **equal** resistors in each emitter make up for small differences in β or transistor area, and reduce the effect of supply changes. You often see these in datasheet block diagrams. They are fine for **small currents** (like 100 µA), where a resistor wastes almost no power.

**Choosing the value (rule of thumb from the video):** drop about **10–20 % of the supply** across them.

$$
R_E \approx \frac{(0.1 \text{ to } 0.2)\,V_{CC}}{I}
$$

### 6.2 Wilson current mirror

Wilson's idea: replace those resistors with **extra transistors**, which take less area than resistors. The result is a mirror that is **more accurate** and needs **no resistors**. This is the version that is widely used. Its error from finite β is much smaller than in the basic mirror:

$$
I_{out} = I_{ref}\left(1 - \frac{2}{\beta^2 + 2\beta + 2}\right)
$$

<!-- Add figure: media/ep33-wilson-current-mirror.png -->

---

## 7. Worked example (basic mirror)

- Supply: $V_{CC} = 15\ \text{V}$
- Diode-connected transistor drop: $V_{BE} \approx 0.6\ \text{V}$ → $14.4\ \text{V}$ across the resistor
- $R = 14.4\ \text{k}\Omega$

$$
I_{ref} = \frac{14.4\ \text{V}}{14.4\ \text{k}\Omega} = 1\ \text{mA}
\quad\Rightarrow\quad I_{out} = 1\ \text{mA}
$$

The load (LEDs, a motor, a whole sub-circuit…) gets 1 mA with **no resistor in its branch**.

---

## 8. Spotting mirrors in real schematics

Exercises from the video:

| Circuit | What's inside |
|---|---|
| Circuit 1 | Two mirrors. One copies **1 mA** (set by $15\ \text{V} / 15\ \text{k}\Omega$) into a branch feeding a whole sub-circuit. The second fixes the current between two other branches. |
| Circuit 2 | A **150 µA current source** feeds a stage. A mirror at the top (with 100 Ω emitter resistors) makes two currents that add up to **2 mA**. |
| Two diodes biasing two transistor bases | **Not a mirror.** It's a **current source**: no collector is tied to the shared base. |
| Resistor setting the input current | A mirror. The input resistor can be replaced with a **current source**, which sets the input current and gets copied to several outputs. |
| LM317 + 120 Ω on the input | The LM317 holds $1.25\ \text{V}$ across 120 Ω, so $I = 1.25/120 \approx 10.4\ \text{mA}$. That current is mirrored to the outputs. |

**How to tell them apart:** in a mirror, the **shared base line is connected to the collector of one of the transistors** (the diode-connected one). Current source and current mirror are often used **together**: the source sets the input current, and the mirror copies it as many times as needed.

---

## 9. Practical test (discrete transistors)

Starting point: the **25 mA constant current source** from [ep32](../ep32-constant-current-source/) (9 V supply). Instead of driving the LEDs directly, its current goes into a mirror built from **two separate 2N2222** transistors. A white LED is the load.

| Test | Expected | Measured | Notes |
|---|---|---|---|
| Basic mirror, no emitter resistors | 25 mA | **120 mA** | Discrete transistors aren't matched, so the copy is way off. |
| Add 75 Ω in each emitter | 25 mA | **26 mA** | Mismatch fixed. $25\ \text{mA} \times 75\ \Omega \approx 1.9\ \text{V}$ across each resistor. |
| Wilson mirror (2 extra transistors, no resistors), 1 LED | 25 mA | **26 mA** | Accurate without any resistor. |
| Wilson mirror, 2 LEDs in series | 25 mA | **26 mA** | The current doesn't depend on the load, as long as the supply has enough headroom. |
| Supply raised from 9 V to 13 V | 26 mA | **29 mA** | The **current source** itself rose to 29 mA (measured). The mirror copied it correctly. |

### What I learned

- A basic mirror made from **discrete** transistors can be badly off (120 mA vs 25 mA) because the parts aren't matched.
- Small **emitter resistors** are a simple fix for discrete builds.
- The **Wilson** mirror gives an accurate copy with **no resistors**, which is why it's popular in IC design.
- When the output changes, check the **reference source** first. Here the mirror was fine; the source had drifted with the supply.

---

## 10. Summary: the new "box"

**Current mirror:** two (or more) matched transistors with shared bases, one of them diode-connected. It **copies (or scales) a reference current** into other branches **without resistors in the load path**. Variants:

- basic mirror
- mirror with emitter degeneration resistors
- Wilson mirror

Once you recognize this block in an IC's internal diagram, you know what that part of the chip is doing.

---

## Files

- `media/` – Circuit Wizard simulations and figures (to be added)
