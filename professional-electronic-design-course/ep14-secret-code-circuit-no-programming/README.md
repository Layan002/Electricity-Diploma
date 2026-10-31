# Ep14 – Designing a Secret-Code Circuit Without Programming

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [تصميم دائرة كود سري من غير برمجة (Designing a secret-code circuit without programming)](https://www.youtube.com/watch?v=-NGo9LkL8vQ) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

This is the first episode of **Level 2: Advanced Electronic Design**. In this level, circuits start to "decide" for themselves: they turn a load on or off based on their inputs, **with no programming and no microcontroller**, only basic parts.

**The task:** a homeowner wants to lock a room with an **electronic lock** (a solenoid). There are ten separate switches labelled **0–9**. The door must open **only** when switches **4, 5, 6 and 8** are all on, and it must **stay locked** if:

- any of those four is missing (wrong code), or
- any **extra** switch is also on (e.g. 4, 5, 6, 8 **and** 9).

**Given parts:** a 9 V or 12 V battery, a **12 V solenoid** (the lock), ten fully independent switches. Any basic parts are allowed (diodes, LEDs, transistors, resistors, capacitors, inductors). The instructor encourages using a **MOSFET** this time instead of an NPN/PNP.

**Switch type:** the switches are **latching on/off** (toggle) switches. You flip each one and it stays on. With push buttons you'd have to hold four buttons down at once.

**Prerequisites:** how the transistor works, and the **MOSFET** from the Practical Electronics Course (gate, gate capacitance, $V_{GS(th)}$, $R_{DS(on)}$).

---

## 1. Designing It Step by Step

The design is built up in four steps. Each step fixes a problem found in the one before.

### Step 1: The four correct switches in series → solenoid

Solenoid to ground, and +12 V reaches it only through switches 4 → 5 → 6 → 8 **in series**.

- **Series**, not parallel: in parallel, *any one* switch would open the lock. In series, current flows only when **all four** are closed (an AND condition).

| Goal | Met? |
|---|---|
| All four correct switches → open | ✔ |
| Missing or wrong code (e.g. only 4, 5, 8) → locked | ✔ |
| Extra switch also on (e.g. 4, 5, 6, 8 + 2) → locked | ✘ The other switches aren't connected to anything, so they're simply ignored |

So anyone who flips **all ten** switches gets in. This design isn't good enough.

### Step 2: Put a MOSFET between the solenoid and ground

Instead of feeding the solenoid directly, the series chain drives **something before the lock** that can be turned **on** by the right switches and **off** by any wrong one. A relay, a transistor or another sensor could do it. Here it's an **N-channel MOSFET** used as a **low-side switch**:

- Solenoid: one end on **+12 V**, the other end on the MOSFET **drain**.
- MOSFET **source** on ground.
- The end of the 4-5-6-8 chain now goes to the MOSFET **gate**.

**Always add a gate pull-down resistor** (gate → ground, **10 kΩ to 1 MΩ**). A MOSFET gate is basically a small **capacitor**. Once it's charged it stays charged, so the MOSFET can stay on after the drive is removed (even touching the gate with a finger can switch it). The pull-down discharges the gate so the MOSFET is really **off** when nothing drives it.

Result: the correct code turns the lock on, but extra switches still don't do anything yet.

### Step 3: Wrong switches pull the gate to 0 V (and short the battery ✘)

A MOSFET turns off when its gate is at **0 V**. So every **wrong** switch (0, 1, 2, 3, 7, 9) is wired from **ground** to the **gate**. Flipping any of them forces the gate to 0 V.

The problem: if the correct code is also on, the gate is connected to **+12 V** through 4-5-6-8 **and** to **ground** through the wrong switch, at the same time. That's a **short circuit across the battery**:

- a **lithium** battery can overheat or even explode,
- a lead-acid or ordinary battery drains completely within minutes.

The lock does stay shut, but the design is unsafe.

### Step 4: A 10 kΩ series resistor stops the short ✔

Put a **10 kΩ resistor** between the end of the correct-switch chain and the gate. The gate still gets enough voltage, but there's never a direct path from +12 V to ground.

**Correct code, no wrong switches:** the series 10 kΩ and the 10 kΩ pull-down form a voltage divider (the gate draws no DC current):

$$V_G = 12\ \text{V} \times \frac{10\ \text{k}\Omega}{10\ \text{k}\Omega + 10\ \text{k}\Omega} = 6\ \text{V}$$

6 V is well above the 2N7000's $V_{GS(th)}$ (0.8–3 V), so the MOSFET turns on and the lock opens.

**Correct code + any wrong switch:** the gate is tied straight to ground → $V_G \approx 0\ \text{V}$ → lock stays shut. The only current that flows is through the series resistor:

$$I = \frac{12\ \text{V}}{10\ \text{k}\Omega} = 1.2\ \text{mA}$$

That's tiny, so the battery is safe.

| Switches on | Gate voltage | Lock |
|---|---|---|
| None, or only some of 4/5/6/8 | 0 V (pull-down) | Locked |
| 4, 5, 6, 8 only | ≈ 6 V | **Open** |
| 4, 5, 6, 8 + any wrong switch | ≈ 0 V (1.2 mA through 10 kΩ) | Locked |
| Wrong code (e.g. 0, 5, 6, 8) | 0 V | Locked |

All three goals are met by adding just **one MOSFET and two resistors** to the basic series chain.

---

## 2. The Hardware Build

| Part | Value / type |
|---|---|
| Supply | 12 V |
| Lock | 12 V solenoid (the plunger pulls in to unlock the door) |
| Switch element | **2N7000** N-channel MOSFET (low side) |
| Gate series resistor | **10 kΩ** (prevents the short) |
| Gate pull-down | **10 kΩ** (discharges the gate) |
| Flyback diode | silicon diode **reversed across the solenoid** (needed with any coil: motor, relay, solenoid) |
| Switches | 10 × latching on/off switches in two rows: 0–4 and 5–9 |

**Test on the board:** 4 → 5 → 6 → 8 on → the MOSFET turns on and the solenoid pulls in. Adding 7, 0 or 1 → it releases at once. Starting with 0, 5, 6, 8 (no 4) → nothing happens.

**Limitation:** the switches stay where you put them, so after opening the door someone has to **scramble them again**, like the dials on a luggage combination lock. That's fine for a small safe or a kids' learning project, but it's not great for a room door.

---

## 3. BJT or MOSFET? Three Questions to Ask

A professional designer should be able to say *why* they picked a part. Before choosing between a **BJT** (NPN/PNP) and a **MOSFET**, ask these three questions.

### Q1: Does the on/off switching need to be fast?

A chart from a textbook shown in the video compares operating frequencies:

- **BJT:** low frequencies, roughly 0 to about 10 kHz.
- **MOSFET:** up to roughly 1000 kHz.

If the circuit must switch very fast (e.g. **100 kHz**), use a **MOSFET**. A door lock doesn't care about a second either way, so **both** are fine here.

*What about an IGBT?* It's expensive and made for **high voltages and currents**. It isn't really available for small 12 V, low-current jobs like this, so it makes no sense here.

### Q2: How much power is lost as heat?

The power a transistor wastes as heat should be as small as possible.

$$P_{BJT} = V_{CE(sat)} \times I_C \qquad\qquad P_{MOSFET} = I_D^{\,2} \times R_{DS(on)}$$

For a BJT you must make sure it's in **saturation**. Values from the datasheets of two parts from the instructor's lab:

| | **BC547B** (BJT) | **2N7000** (MOSFET) |
|---|---|---|
| Key loss parameter | $V_{CE(sat)}$ = 0.2–0.6 V at 100 mA → worst case **0.6 V** | $R_{DS(on)}$ ≈ **5 Ω** |
| Max current | 100 mA | ≈ 200 mA drain current |
| Turn-on voltage | $V_{BE(on)}$ ≈ 0.6–0.7 V | $V_{GS(th)}$ = 0.8–3 V |

With the lock drawing **100 mA**:

$$P_{2N7000} = (0.1\ \text{A})^2 \times 5\ \Omega = 50\ \text{mW}$$

$$P_{BC547B} = 0.6\ \text{V} \times 0.1\ \text{A} = 60\ \text{mW}$$

These are almost the same, so neither is clearly better here. The difference only matters when it's large, e.g. a BJT with $V_{CE(sat)}$ = 0.2 V (≈ 20 mW) or a MOSFET with a much smaller $R_{DS(on)}$.

### Q3: What voltage drives the input?

Know where the gate/base signal comes from (a microcontroller, a big circuit, a low-voltage circuit) so you know whether the transistor will actually turn on.

- BJT: needs $V_{BE}$ ≈ **0.7 V**.
- 2N7000: needs $V_{GS(th)}$ = **0.8–3 V**, and more than that to turn fully on.

If the drive voltage is low, the MOSFET might not turn on, so it would be the worse choice. Here the gate gets **6 V** from a 12 V supply, so both work.

**Conclusion:** for this project, a BJT and a MOSFET are equivalent on all three questions. The MOSFET was chosen to try a different design style. If a BJT does the job, a BJT is fine.

---

## 4. Other Ways to Build the Same Circuit

The instructor showed a few versions found online that use the same idea:

- **NPN version:** the correct switches in series feed the transistor **base** through resistors (to limit the base current), and all wrong switches connect the base to **ground**, cancelling any drive from the correct chain. The load (lock) is on the collector side.
- **DIP-switch version:** a small **DIP switch** block replaces the big toggle switches, and a **relay** replaces the solenoid, so it can switch anything. Here +12 V passes through a resistor (to prevent the short), then switches **1 → 3 → 6** to the base. Every other switch grounds the base, and a **10 kΩ** resistor holds the base at ground when nothing drives it.

Most people would reach for a microcontroller, programming or an IC for this task. Thinking in terms of **series = AND**, **"pull to 0 V" = veto**, and **a resistor so that veto can't short the supply** solves it with three or four parts.

---

## My Circuit Wizard Circuit

📁 Circuit file: [ep14-secret-code-lock.cwz](./ep14-secret-code-lock.cwz)

Parts in the file (from its parts list):

| Part | Qty |
|---|---|
| 12 V battery (ideal) | 1 |
| N-channel MOSFET (ideal) | 1 |
| Solenoid (6 V DC) | 1 |
| 10 kΩ resistor (¼ W) | 2 (series gate resistor + gate pull-down) |
| SPST switch | 10 (digits 0–9) |

The parts match the Step 4 design from section 1 (one MOSFET + two 10 kΩ resistors). Two differences from the instructor's hardware build:

- Circuit Wizard's solenoid is the **6 V** model, while the video uses a **12 V** solenoid. On a 12 V supply it gets about twice its rating, which is fine in a simulation but not on real hardware.
- The file has **no flyback diode** across the solenoid. In the simulation this doesn't matter much, but any real build needs one.

**Expected in the simulation:**

| Switches | Gate voltage | Solenoid |
|---|---|---|
| 4, 5, 6, 8 | ≈ 6.00 V | on |
| 4, 5, 6, 8 + any other | ≈ 0 V, ≈ 1.2 mA through the series 10 kΩ | off |
| Wrong code | 0 V | off |

*(No simulation recording or screenshot yet. I'll add it to `media/` with the measured values.)*

---

## What I Learned

- **Series switches = AND:** current only flows when all of them are closed. Parallel switches = OR.
- Ignoring extra inputs isn't the same as rejecting them. The wrong switches have to do something active: **pull the gate to 0 V**.
- Pulling a node to ground while the supply is also connected to it is a **battery short**. A **series resistor** turns that short into a harmless 1.2 mA while still letting the gate reach 6 V.
- A MOSFET gate is a capacitor: always fit a **gate pull-down** (10 kΩ–1 MΩ), or the MOSFET can stay on by itself.
- Coils (solenoids, relays, motors) always need a **reversed diode** across them.
- Choosing BJT vs. MOSFET comes down to **switching speed**, **power loss** ($V_{CE(sat)} I_C$ vs. $I_D^2 R_{DS(on)}$) and **drive voltage** vs. $V_{BE}$ / $V_{GS(th)}$. For this lock they're equivalent (≈ 60 mW vs. ≈ 50 mW).

### Next to try

- Record the simulation: correct code → solenoid on; add a wrong switch → off. Measure the gate voltage (expect ≈ 6 V) and the current in the series 10 kΩ (expect ≈ 1.2 mA).
- Remove the series 10 kΩ and add an ammeter to see how large the short-circuit current gets.
- Remove the pull-down resistor and see whether the ideal MOSFET stays on after the code is broken.
- Add a flyback diode across the solenoid and use a 12 V solenoid model (or a 6 V supply).
- Swap the MOSFET for a BC547B + base resistor and compare the load voltage and power loss.
