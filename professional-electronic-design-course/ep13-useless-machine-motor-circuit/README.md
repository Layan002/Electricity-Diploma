# Ep13 – Designing a Useless Machine Motor Circuit

**Course:** Professional Electronic Design Course – Level 1 (Simple Electronic Design)
**Source:** Waleed Issa Academy – [تصميم دائرة محرك مالهاش لزمة (Designing a useless machine motor circuit)](https://www.youtube.com/watch?v=U1TNPuQPPw8) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

**The task:** build the famous **"useless machine"** (useless box). You flip a switch on, a motor-driven arm comes out, flips the switch back off, and then the arm goes back home and stops.

The episode builds up to it in three steps:

1. What **ground** really means in a circuit (and in a simulator).
2. Using two batteries in series with the ground in the middle to get **+V and −V**, and why splitting one supply with two capacitors doesn't work.
3. A **complementary transistor pair** (NPN + PNP) that drives a motor forward or backward from one control signal. Then the design task: add **one part** so the motor stops by itself when it gets home.

**Prerequisites:** the transistor switch, NPN vs. PNP, the Zener diode as a threshold (ep9–ep10), and the push-pull pair from [ep8](../ep08-transistor-circuits-light-bulb-factory/).

---

## 1. What "Ground" Means

Voltage is always measured **between two points**. The ground symbol is just the designer saying: *"I call this point 0 V."* Every other voltage in the circuit is then given relative to it.

- The battery doesn't care where you put the ground. It only keeps its two terminals a fixed voltage apart.
- In a simulator, the ground symbol tells the program which node is the 0 V reference. **All ground symbols in a schematic are the same node**, so two ground symbols are the same as a wire between those two points.
- In a battery-powered circuit there's usually no "real" earth. We normally pick the **battery negative** as 0 V and call it ground.

### Example: four equal resistors on 12 V

Four 1 kΩ resistors in series across 12 V each drop a quarter of the supply:

$$V_{R} = \frac{12\ \text{V}}{4} = 3\ \text{V each}$$

The voltage of the **top node** depends on where you put 0 V:

| Ground placed at | Top node reads |
|---|---|
| Below R1 (1 resistor down) | 3 V |
| Below R2 (2 resistors down) | 6 V |
| Below R3 (3 resistors down) | 9 V |
| Battery negative (bottom) | 12 V |

The circuit itself doesn't change. Only the reference does.

### Why it matters: a Zener + transistor LED branch

The instructor adds an LED driver to the same divider: tap node → **3.9 V Zener** → base resistor → NPN → LED to +12 V, with the emitter on ground. To turn on, the tap must sit about $V_Z + V_{BE} \approx 3.9 + 0.7 \approx 4.6\ \text{V}$ above ground, plus a little for the base resistor (≈ 5 V in total).

- Ground placed so the tap is only **3 V** above it → LED **off**.
- Ground moved down so the tap is **6 V** above it → LED **on**.

Next, the instructor puts a 1.5 V battery in the emitter path. Now the branch needs about $4 + 0.7 + 1.5 \approx 6\ \text{V}$, so the tap at 6 V is right on the edge and the LED stays off. Moving the tap up, or the ground down (tap at 9 V), fixes it. In the real circuit there's no "ground wire" as such: putting a ground symbol at two places just means **connecting those two points with a wire**.

---

## 2. A ± Supply from Two Batteries

Two batteries in series with the **middle point as ground**:

- The top battery's + is 12 V above the middle → **+12 V**.
- The bottom battery's + terminal *is* the middle (0 V), so its − terminal must be 12 V lower → **−12 V**.

So two 12 V batteries give **+12 V, 0 V, −12 V** (two 9 V batteries give ±9 V).

### Why not split one battery with two capacitors?

Two equal capacitors in series across 12 V each charge to 6 V, so the middle reads +6 V / −6 V **with no load**. As soon as a load (e.g. 1 kΩ) is connected to one side, it drains that capacitor: one side drops toward 0 V and the other rises toward −12 V. The midpoint isn't held anywhere. You'd need huge capacitors (1–3 F), which are as big as a battery anyway. **Use two batteries.**

---

## 3. Reversing a DC Motor with a Complementary Pair

A DC motor reverses when you reverse its polarity. With one motor terminal on ground (0 V):

- **+12 V** on the other terminal → turns one way (e.g. forward).
- **−12 V** on the other terminal → turns the other way.

A switch can do this directly, but we want a **signal** (later a sensor) to choose the direction. So:

- **Q1 (NPN)**: collector to +12 V, emitter to the motor.
- **Q2 (PNP)**: collector to −12 V, emitter to the motor.
- Both **bases joined** and fed from a single control line through a **1 kΩ** resistor.

| Control signal | Q1 (NPN) | Q2 (PNP) | Motor gets | Direction |
|---|---|---|---|---|
| Near +12 V | On | Off | ≈ +11.3 V | Forward |
| Near −12 V | Off | On | ≈ −11.3 V | Back |

Only one transistor can conduct at a time. Each one works as an emitter follower, so the motor gets about $V_{supply} - 0.7\ \text{V}$. Replace the switch with an LDR or thermistor divider and a sensor can choose the direction.

### Direction LEDs

- **Forward LED** (red): from the motor line to ground in the normal direction, with a resistor. It lights on **+12 V**.
- **Return LED** (green): same, but **reversed**. On −12 V the ground end is 12 V *higher* than the motor end, so current flows the other way and this LED lights.

An LED only cares that one end is higher than the other, not whether the numbers are positive or negative.

---

## 4. Design Task: the "Angry Motor" (Useless Machine)

**Brief:** the motor drives an arm. When you flip the switch, the motor gets "angry", the arm flips the switch back, and the arm returns home **and stops**. Make the smallest possible change to the circuit above.

### The problem

The main toggle switch picks + or −. Flipping it on sends the motor forward; the arm flips it back, so the motor gets −12 V and reverses… **forever**. Nothing tells it to stop when it reaches home.

### The idea: a second switch, used as a limit sensor

The motor is strong enough to press one switch, so it can press another one on the way back. Where to put it?

- **In the common line** (before the bases, or in the motor line): when pressed, it cuts *both* + and −. The motor can then never start again, so that's a bad choice.
- **In the −12 V line only** ✔. The negative supply is what drives the **return**. Cutting it stops the return without touching the forward path.

### Normally closed (NC), not normally open

When the arm is at home it's pressing the limit switch, and the motor must be **off**. So the switch must be **open when pressed** and closed when released: a **normally closed (NC)** switch.

The full cycle:

1. **At rest:** arm at home, limit switch pressed (open), main switch in the "off" (−) position → no current, motor stopped.
2. **Flip the main switch on:** the control line goes +, Q1 turns on, motor goes forward, red LED on. The arm leaves home, so the limit switch closes again (ready for the return).
3. **Arm hits the main switch:** it's flipped back to −. Q2 turns on, motor reverses, green LED on.
4. **Arm reaches home:** it presses the limit switch, the −12 V line opens, Q2 loses its base drive, motor stops, both LEDs off.

Pressing the switch again even while the arm is halfway back restarts the cycle.

### Component values (hardware build)

| Part | Value |
|---|---|
| Supply | 2 batteries in series, midpoint = ground (± supply) |
| NPN (forward) | **BC547** |
| PNP (return) | **BD140** |
| Base resistor | **1 kΩ** |
| Motor | small 5 V DC motor + 3 × 100 Ω in parallel (≈ 33 Ω) in series to drop the extra voltage |
| Main switch | ON-ON toggle (SPDT): middle pin = common, flips between + and − |
| Limit switch | lever micro switch (COM / NC / NO), wired **COM + NC** only, glued where the arm presses it at home |
| LEDs | red = going out, green = returning, each with a resistor |

On the hardware: press → red (arm goes out and flips the switch) → green (arm comes back) → both off (home, stopped). It works every time, even if you flip the switch again halfway.

Small changes like one NC switch, placed in the right line, turn a basic circuit into a working product.

---

## My Circuit Wizard Simulations

All files are in [`media/`](./media/). The GIFs play on this page (1.5× speed, cropped to the circuit). Click the MP4 links for the full recordings.

### 1. Ground as a reference: the voltmeter on the 4-resistor divider

![Ground reference divider](media/ep13-sim-ground-reference-divider.gif)

*The voltmeter's + lead stays on the top node, and its − lead (its own 0 V reference) is moved to different points down the divider. Watch the reading jump between 3.00 V, 12.00 V, 9.00 V and 6.00 V while the circuit itself never changes.* · [MP4](media/ep13-sim-ground-reference-divider.mp4)

- **Setup:** B1 = 12 V; R1–R4 = 1 kΩ in series; voltmeter + on the top of R1. (No separate circuit file: it's the same divider as in sim 2, without the LED branch.)

| Voltmeter − lead at | Resistors spanned | Expected | Simulated |
|---|---|---|---|
| Below R1 | 1 | 3 V | **3.00 V** ✔ |
| Below R4 (battery −) | 4 | 12 V | **12.00 V** ✔ |
| Below R3 | 3 | 9 V | **9.00 V** ✔ |
| Below R2 | 2 | 6 V | **6.00 V** ✔ |

### 2. Moving the ground: Zener + transistor LED branch

![Ground position Zener LED](media/ep13-sim-ground-position-zener-led.gif)

*The divider's ground symbol is moved between nodes, and that changes how many volts the tap sits above the transistor's emitter (which is also on ground). Watch LED D2: on with the tap 9 V above ground, off at 3 V and 6 V. Halfway through, the Zener's model is changed from "Ideal" to 1N751 (5.1 V). At the end, D1 is rewired to the top rail and the LED comes back on.* · [MP4](media/ep13-sim-ground-position-zener-led.mp4)

📁 Circuit file: [ep13-ground-position-zener-led.cwz](./ep13-ground-position-zener-led.cwz) (final state: 1N751 Zener, D1 wired to the top rail)

- **Setup:** B1 = 12 V; R1–R4 = 1 kΩ divider; tap between R1 and R2 → Zener D1 (reverse biased) → R5 = 1 kΩ → Q1 (NPN) base; Q1 collector → R6 = 220 Ω → LED D2 → +12 V; Q1 emitter to ground. The divider's ground symbol is moved.

The tap is loaded by the Zener branch, so I used its Thévenin equivalent (R1 against everything below it down to ground):

| Ground placed at | Tap unloaded ($V_{th}$) | $R_{th}$ | Zener model | LED D2 |
|---|---|---|---|---|
| Battery − (bottom) | 9 V | 1 kΩ ∥ 3 kΩ = 750 Ω | Ideal | **On** ✔ |
| Between R2 and R3 | 3 V | — | Ideal | **Off** ✔ |
| Between R3 and R4 | 6 V | 1 kΩ ∥ 2 kΩ ≈ 667 Ω | Ideal | **Off** |
| Between R3 and R4 | 6 V | ≈ 667 Ω | 1N751 (5.1 V) | **Off** |
| Battery − (bottom) | 9 V | 750 Ω | 1N751 (5.1 V) | **On** ✔ |
| Between R3 and R4, D1 moved to the top rail | 9 V | ≈ 0 Ω | 1N751 (5.1 V) | **On** ✔ |

**Checking the "on" case** (ground at the bottom): the tooltip on the tap wire read **7.66 V** and **2.55 mA** going down R2. So

$$I_{R1} = \frac{12 - 7.66}{1\ \text{k}\Omega} = 4.34\ \text{mA}, \qquad I_{R2..R4} = \frac{7.66}{3\ \text{k}\Omega} = 2.55\ \text{mA}\ ✔$$

$$I_B = 4.34 - 2.55 \approx 1.79\ \text{mA}$$

$$V_Z \approx 7.66 - I_B R_5 - V_{BE} \approx 7.66 - 1.79 - 0.7 \approx 5.2\ \text{V}$$

So the "Ideal" Zener in Circuit Wizard behaves like a ≈ 5.1 V part, which is why swapping in a 1N751 didn't change anything. The base current is far more than the ≈ 45 mA / β the LED needs, so Q1 is saturated.

**Why 6 V isn't enough:** the branch needs about $V_Z + V_{BE} = 5.1 + 0.7 = 5.8\ \text{V}$ before any base current flows. The tap only reaches 6 V with no load, so at best

$$I_B \approx \frac{6 - 5.8}{667\ \Omega + 1\ \text{k}\Omega} \approx 0.1\ \text{mA}$$

That's right on the edge, the same as the instructor's "almost but not quite" case. The instructor used a 3.9 V Zener, so his 6 V case turned on. Mine has a 5.1 V Zener, so it doesn't. Moving the Zener to the top rail (9 V, no divider resistance in the way) gives plenty of margin, and the LED lights.

### 3. The useless machine circuit with the NC limit switch

![Useless machine limit switch](media/ep13-sim-useless-machine-limit-switch.gif)

*SW1 picks +9 V or −9 V for the transistor bases; SW2 (the limit switch) sits only in the −9 V line. Watch the motor MT1 and the LEDs: up → D1 lights and the motor turns forward; down → D2 lights and the motor reverses; opening SW2 (the arm "pressing" the limit switch at home) stops everything, both LEDs off.* · [MP4](media/ep13-sim-useless-machine-limit-switch.mp4)

📁 Circuit files:
- [ep13-useless-machine.cwz](./ep13-useless-machine.cwz): the final design (1 kΩ base resistor + SW2 limit switch in the −9 V line)
- [ep13-motor-reversing-pair.cwz](./ep13-motor-reversing-pair.cwz): the starting circuit before the design task (±9 V, SW1, NPN/PNP pair, motor, two LEDs). It has no limit switch or base resistor, so the motor never stops on its own.

- **Setup:** B1 = B2 = 9 V in series, midpoint to ground (±9 V); SW1 (SPDT) common → R3 = 1 kΩ → bases of Q1 (NPN, collector to +9 V) and Q2 (PNP, collector to −9 V); SW1's lower contact goes to −9 V **through SW2**; emitters joined → motor MT1 to ground; D1 + R1 = 220 Ω (lights on +), D2 + R2 = 220 Ω reversed (lights on −).
- The motor in both files is Circuit Wizard's **"DC Motor (3V DC)"**, so in the sim it gets ≈ 8.3 V, almost 3× its rating. The instructor solved the same problem on hardware with a series resistor (3 × 100 Ω in parallel ≈ 33 Ω) in front of his 5 V motor.
- I toggle SW2 by hand to stand in for the arm pressing it. In the real machine it's an NC micro switch, so "pressed" = open.

| SW1 | SW2 (limit) | Motor gets (expected) | Simulated |
|---|---|---|---|
| Up (+9 V) | either | ≈ +8.3 V → forward | D1 on, motor turns ✔ |
| Down (−9 V) | closed (arm away from home) | ≈ −8.3 V → back | D2 on, motor reverses ✔ |
| Down (−9 V) | open (arm home, pressing it) | 0 V | both LEDs off, motor stopped ✔ |

**Expected LED current** (emitter-follower output ≈ 9 − 0.7 = 8.3 V, red LED ≈ 2 V):

$$I_{LED} \approx \frac{8.3 - 2}{220\ \Omega} \approx 29\ \text{mA}$$

That's fine for a short demo, but it's above the usual 20 mA. In a build I'd use ≈ 330 Ω.

---

## What I Learned

- **Ground is just the point I call 0 V.** Every voltage is measured between two points; moving the reference changes the numbers, not the circuit.
- All ground symbols in a schematic are **one node**. Two grounds = a wire between those points.
- Whether a Zener + transistor branch turns on depends on how many volts the tap sits **above the emitter**: it needs about $V_Z + 0.7\ \text{V}$ plus the drop across the base resistor.
- **Two batteries in series with the middle grounded** give a ± supply. A capacitor divider can't hold its midpoint once you connect a load.
- A **complementary NPN/PNP pair** with joined bases and emitters steers + or − to a motor, so one control line sets the direction.
- An LED fed from −V just needs to be **reversed**: it only cares that one end is higher than the other.
- **Useless machine fix:** one **normally closed** switch in the **−V (return) line only**. It stops the return when the arm gets home without blocking the next forward start.
- **From my simulations:** the voltmeter read exactly 3 / 6 / 9 / 12 V. The LED branch switched on only with the tap 9 V above ground (≈ 5.1 V Zener). The ±9 V motor circuit went forward, back, and stopped on SW2 as designed.

### Next to try

- Add a series resistor in front of the 3 V motor (like the instructor's ≈ 33 Ω dropper) so it gets ≈ 3 V instead of ≈ 8.3 V.
- Add a voltmeter across MT1 and record the actual ± motor voltage (expect ≈ ±8.3 V minus the drop across R3).
- Change R1/R2 to 330 Ω to keep the LEDs under 20 mA.
- Repeat sim 2 with a 3.9 V Zener (like the instructor) and check that the 6 V case now turns on.
- Simulate the capacitor-split supply (2 × capacitors across 12 V, 1 kΩ load) and watch the midpoint drift.
