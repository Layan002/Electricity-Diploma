# Ep23 – Designing a Random-Selection Circuit Using the Oscillator

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 23- تصميم دائرة للاختيار العشوائي عن طريق المذبذب (Designing a random-selection circuit using the oscillator)](https://www.youtube.com/watch?v=ssALZZzLdJk&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=161) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

Ep22 built a simple oscillator from the **flip-flop capacitor** idea, but its ON and OFF times were hard to control. This episode introduces the most famous two-transistor oscillator, the **astable multivibrator** (also called the **transistor flasher**), which later projects in the course build on.

The episode has four parts:

1. A **one-transistor demo** that shows the flip-flop capacitor in slow motion.
2. The **two-transistor flasher**: how it starts, how it switches, and how R and C set the timing.
3. **Variations from the internet**: a light-activated buzzer, a louder speaker connection, a toy organ, and the oscillator used as a "black box".
4. A **design challenge**: a "decision helper" that flashes two LEDs while you hold a button and **stops on one of them at random** when you let go.

Before this episode you need to understand the **transistor as a switch**, **capacitor charging**, the **flip-flop capacitor** (ep22), and the **transistor latch** ([ep18](../ep18-transistor-latch-with-delay/), [ep19](../ep19-smart-mailbox-letter-detector/)).

> "Flip-flop capacitor" is the instructor's own teaching name. It isn't a standard textbook term, but it makes the flasher much easier to follow than the usual textbook explanation.

---

## 1. The Flip-Flop Capacitor in Slow Motion

### What a flip-flop capacitor is

A capacitor is charged so that one plate is positive and the other negative. Then one plate is suddenly pushed to a **different voltage**. The capacitor can't change its charge instantly, so the **other plate jumps by the same amount**, and can even go **negative**. Then it slowly recharges the other way. That "flip" of the charge gives a **time delay**.

### The demo circuit

- **9 V** supply.
- **Q1** with an LED and resistor in its collector, and a **push switch** that feeds its base.
- A capacitor from **Q1's collector** to **Q2's base**.
- **Q2** has its own LED in the collector, and a resistor from +9 V to its base.

### Before the button is pressed

| Point | Voltage | Why |
|---|---|---|
| Q1 collector (left plate) | ≈ 8.5 V | Q1 is off. The current can't find a path to ground, so this node sits near the supply. |
| Q2 base (right plate) | ≈ 0.7 V | Q2 is on, and its base–emitter junction **clamps** this plate at about 0.7 V. |

So the capacitor holds about:

$$
V_C \approx 8.5\ \text{V} - 0.7\ \text{V} \approx 8\ \text{V} \quad (\text{left plate positive})
$$

One plate of a flip-flop capacitor always needs something that **fixes its voltage**. Here that's Q2's base–emitter junction.

### Press the button

1. Q1 turns on and pulls its collector (the left plate) from ≈ 8.5 V down to **0 V**.
2. The capacitor still holds 8 V, with the left plate 8 V **above** the right plate. If the left plate is 0 V, the right plate must jump to about **−8 V** (the simulation showed ≈ **−7 V**).
3. Q2's base is now negative → **Q2 turns off**, and its LED goes out.
4. Current flows through Q2's base resistor into the capacitor and slowly **charges it the other way**: −7 V → −5 V → −3 V → −1 V → 0 V → 0.2 … 0.6 V.
5. At ≈ **0.7 V**, Q2 turns **back on** and its LED lights again.

The time between steps 3 and 5 is a **delay** made by one transistor flipping the capacitor.

### Release the button

Q1 turns off. Its collector rises back to ≈ 9 V, and the capacitor charges back to its original state (left plate high, right plate at 0.7 V), ready for the next press.

**Simulation tip from the video:** set Circuit Wizard's **time control to about 1 ms** so you can watch the voltages change step by step.

### The key idea

Replace the push button with **another transistor**, driven the same way, and each transistor can switch the other one off. That's the flasher.

---

## 2. The Two-Transistor Flasher (Astable Multivibrator)

### The circuit

Two identical halves:

- Each transistor has an **LED + resistor** in its collector (the load).
- Each base gets current through a **base resistor** from +V.
- A **capacitor** connects each base to the **other transistor's collector** (cross-coupling).

Neither capacitor goes straight to ground. Each one reaches ground only **through the other transistor**, which is why each one acts as a flip-flop capacitor.

### Who starts first?

The two halves are designed to be identical, but in real life **no two parts are exactly the same**. When power is applied, both bases start receiving current, and one transistor turns on slightly **before** the other. The one that wins **turns the other off**. The circuit depends on this small mismatch to start.

### One full cycle

Say **Q1 turns on first**:

1. Q1's collector falls to ≈ 0 V.
2. The capacitor between Q1's collector and **Q2's base** flips: Q2's base goes negative → **Q2 is off**.
3. Q2's base capacitor now charges through Q2's base resistor, slowly, from negative up toward +0.7 V.
4. Meanwhile, Q2's collector is high, and the capacitor on **Q1's base** charges so that Q1's base sits at 0.7 V.
5. When Q2's base reaches ≈ **0.7 V**, **Q2 turns on**. Its collector drops to 0 V.
6. That flips the capacitor on **Q1's base** → Q1's base goes negative → **Q1 turns off**.
7. Now Q1's base capacitor charges up toward 0.7 V, and the whole thing repeats with the roles swapped.

The two LEDs **take turns**: one ON while the other is OFF. In the simulation (time control ≈ 100 ms), each base can be seen sitting at 0.7 V, then jumping negative, then crawling back up.

### Practical circuit (from the video)

| Part | Value |
|---|---|
| Supply | 9 V |
| Collector resistor (each side) | ≈ 500 Ω (likely the standard 470 Ω) |
| LEDs | red |
| Transistors | 2N2222 (×2) |
| Base resistors | 10 kΩ (×2) |
| Capacitors | 33 µF (×2) |

### Timing: R and C set the speed

The time each transistor stays OFF is the time its base capacitor needs to charge from about −V back up to +0.7 V through its base resistor. So:

- **Bigger C or bigger R** → slower flashing.
- **Smaller C or smaller R** → faster flashing.
- Each half has its **own** R and C, so you can set the **ON time and OFF time separately**. Change $R_1, C_1$ to change one half and $R_2, C_2$ to change the other.

**My note (standard formula, not from the video):** for the classic astable multivibrator, each half-period is about

$$
t_1 \approx 0.693\, R_1 C_1, \qquad t_2 \approx 0.693\, R_2 C_2
$$

$$
T = t_1 + t_2, \qquad f = \frac{1}{T} \approx \frac{1}{1.386\, R C} \ \ (\text{if both halves are equal})
$$

For the practical values above ($R = 10\ \text{k}\Omega$, $C = 33\ \mu\text{F}$):

$$
t \approx 0.693 \times 10\ \text{k}\Omega \times 33\ \mu\text{F} \approx 0.23\ \text{s}, \qquad f \approx 2.2\ \text{Hz}
$$

### Fast flashing: the LEDs look steady, but the scope shows the truth

The instructor changed both 33 µF capacitors to **1 µF**:

- The LEDs flash so fast that both look **always on**. They really are switching, but the eye can't follow.
- On the **oscilloscope**, one collector shows an almost **square wave**, from **0 V to about 6 V**, at about **9.67 Hz**.
- Rule of thumb from the video: the eye can see flicker **below about 50 Hz**; above that it looks steady.

**My note:** with 10 kΩ and 1 µF the formula above predicts about 72 Hz, not 9.67 Hz. The simulation probably used different base resistors from the practical board. I want to check this in my own simulation.

### Output

The LEDs are just one choice of load. You can put a **relay**, a plain **resistor**, or anything else in the collector, and take a **square-wave signal** from either collector to drive another circuit. This is the most common and simplest square-wave oscillator, used in many cheap products to **avoid needing an IC**.

### How to spot it on a schematic

If you see a **base connected to a capacitor**, and that capacitor goes to the **collector of the other transistor** (and the same on the other side), it's an **oscillator**. You should be able to recognise it at a glance, even when it's drawn differently or with different symbols.

---

## 3. Variations Found Online

### a) Light-activated buzzer

- The LEDs are gone. One side has a **47 kΩ** resistor, the other a **speaker**.
- An **LDR** sits in the charging path of one capacitor.
  - **Dark:** the LDR is in the **MΩ** range, so the capacitor can barely charge → **no oscillation**, no sound.
  - **Light:** the LDR drops to **tens of ohms**, as if it weren't there → the circuit **oscillates** and the speaker makes a tone.
- A speaker turns any oscillating signal into sound. A higher frequency gives a higher-pitched tone.
- **Uses:** inside a fridge, a car boot or a cupboard, to remind you that it's open (light gets in), or as an alarm when a light source turns on.

### b) Speaker between the two collectors (louder)

- Charge resistors and load resistors are on each side as usual, but the speaker is connected **between the two collectors**.
- The two collectors are always **opposite** (one high, one low), so the speaker sees a **bigger swing** and the sound is a bit **louder**.
- The pitch is set by the charge/discharge time: here **10 kΩ** and **10 µF**. Change them until you get the tone you want.

### c) Toy organ (keyboard)

- The speaker is connected in yet another place, to show that the output can be taken from more than one point.
- Instead of tying a base resistor straight to the supply, it goes to a **chain of resistors in series**. A movable wire (the "key") picks how many of them are in the path.
- More resistance → longer charging time → lower frequency → **lower note**. Each key gives a different note.
- This is the kind of circuit inside **cheap toy pianos**, with no IC at all.

### d) The oscillator as a "black box"

- Treat the whole oscillator + speaker as one block that has its **+V** but is **missing its ground**.
- Add an **LDR** that drives a **transistor**. When light falls on the LDR, its resistance drops, the transistor turns on, and it **connects the oscillator to ground**, so it starts.
- The LDR + transistor is the **"decision" part** of the circuit: it decides **when** the oscillator runs. The oscillator itself stays a simple, standard block.

---

## 4. Design Challenge: The "Decision Helper"

### The task

Build a circuit for someone who can't decide between two choices (left/right, option 1/option 2, heads/tails):

- **While the button is held**, the oscillator runs and the two LEDs swap very fast, too fast to tell which is on.
- **When the button is released**, the circuit **holds one LED on**: that's the "chosen" option.
- Use only a few extra parts: a switch and some resistors. Hint from the video: remember how a **latch** works.

It can be used as a two-sided coin, a simple draw (option 1 vs option 2), or a dice-like game.

### Step 1 – Add the start switch

What keeps the oscillator running? The **current that charges the two capacitors** through the base resistors. Without it, nothing charges and nothing switches.

So the switch goes in the **supply line of the two base (charging) resistors**:

- Button pressed → the base resistors get current → the oscillator runs.
- Button released → no charging current → the oscillator stops.

(Another option is a switch on one transistor's base to kill it completely, but that just stops the circuit; it doesn't choose anything.)

**Result:** when the button is released, **both LEDs go out**. The oscillator stops, but it doesn't hold a choice. We still need memory.

### Step 2 – Add the latch resistors

A latch means: **each transistor's base is connected through a resistor to the other transistor's collector**. So the instructor added two resistors, each **in parallel with one of the timing capacitors** (base of one transistor ↔ collector of the other).

How it holds the last LED:

1. While the button is held, the oscillator runs as before. The latch resistors are large, so they don't stop the oscillation.
2. When the button is released, the base resistors lose their supply. The only base current left comes through the **latch resistors**.
3. The transistor that was ON at that moment has the OFF transistor's high collector feeding its base through a latch resistor → it **stays ON**.
4. Its own collector is ≈ 0 V, so the latch resistor feeding the OTHER base gives it nothing → the other transistor **stays OFF**.

The circuit settles on **the last LED that was lit**. Because the oscillator is far faster than your finger, that's effectively **random**.

### Values (from the video)

| Part | Value |
|---|---|
| Timing capacitors | 1 µF (×2) |
| Latch resistors | ≈ 46 kΩ (×2), probably meant as the standard 47 kΩ |
| Start switch | push-to-make (or a latching switch, to keep it running) |
| Simulation time control | ≈ 1 s |

The latch resistor value was found **by trial**, not by calculation. 10 kΩ also worked, but around 46–47 kΩ gave a clean hold with **lower current**.

### Simulation (from the video)

- Power on, button not pressed: both LEDs flash briefly at power-up and then go off. Nothing runs.
- Button held: the oscillator runs; both LEDs look lit because the switching is so fast.
- Button released: one LED stays on. Three tries gave **left, left, right**.
- With bigger capacitors (e.g. 33 µF) the flashing becomes slow enough to see that it really **keeps the last LED that was on**.

---

## Summary of Values from the Video

| Circuit | Item | Value |
|---|---|---|
| Flip-flop demo | Supply | 9 V |
| Flip-flop demo | Capacitor voltage before the press | ≈ 8 V (8.5 V − 0.7 V) |
| Flip-flop demo | Q2 base right after the press | ≈ −7 V (ideally −8 V) |
| Flip-flop demo | Q2 turns back on at | ≈ 0.7 V |
| Flip-flop demo | Simulation time control | ≈ 1 ms |
| Flasher | Collector resistors | ≈ 500 Ω (470 Ω) |
| Flasher | Base resistors | 10 kΩ |
| Flasher | Capacitors | 33 µF (slow, visible) / 1 µF (fast) |
| Flasher | Transistors | 2N2222 |
| Flasher | Simulation time control | ≈ 100 ms |
| Flasher (1 µF) | Scope reading | ≈ 0 – 6 V square wave, ≈ 9.67 Hz |
| Flicker limit | Eye can see flicker below | ≈ 50 Hz |
| Light buzzer | Load resistor | 47 kΩ |
| Light buzzer | LDR | MΩ in the dark, tens of Ω in light |
| Louder speaker | Timing R, C | 10 kΩ, 10 µF |
| Decision helper | Timing capacitors | 1 µF |
| Decision helper | Latch resistors | ≈ 46 kΩ (by trial) |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add the flasher and the decision helper here.

---

## What I Learned

- A **flip-flop capacitor** keeps its charge when one plate is suddenly pulled down, so the other plate jumps **negative**. The time it takes to recharge back to +0.7 V is a **delay**.
- The **astable multivibrator** is two cross-coupled transistors: each base gets a resistor from +V and a capacitor from the other collector. It starts because real parts are never perfectly matched.
- **R and C on each side set that side's time**, so ON and OFF times can be set separately ($t \approx 0.693RC$ is the standard estimate).
- At high frequency the LEDs look steady; an **oscilloscope** shows the square wave is still there.
- I should recognise this oscillator **on sight**: base → capacitor → the other transistor's collector, on both sides.
- The same block becomes a **light alarm** (LDR in the charge path), a **louder buzzer** (speaker across both collectors), a **toy organ** (switch between resistor values), or an enabled block (a transistor supplies its ground).
- **Oscillator + latch = random selector.** A switch in the charging supply starts and stops the oscillator, and two latch resistors (base ↔ other collector) hold the last state when it stops.

### Next to try

- Build the 9 V flasher (470 Ω, 2N2222, 10 kΩ, 33 µF) and measure the period. Compare it with $T \approx 1.386RC \approx 0.46\ \text{s}$.
- Change only one side's R or C and measure how the ON and OFF times change.
- Switch to 1 µF and check the frequency on the scope. Find out why the video measured ≈ 9.67 Hz.
- Build the decision helper with 1 µF and 47 kΩ latch resistors. Press and release many times and count how often each LED wins.
- Try latch resistors of 10 kΩ, 47 kΩ and 100 kΩ, and find the largest value that still holds reliably.
