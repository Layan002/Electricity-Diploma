# Ep20 – Designing a One-Press Circuit: Problems and Solutions

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [دورة التصميم الالكتروني المحترف:: 20- تصميم دائرة الضغطة الواحدة مع مشاكل وحلول (Designing a one-press circuit with problems and solutions)](https://www.youtube.com/watch?v=RbrVzHYSXYw&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=158) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

You need [ep18](../ep18-transistor-latch-with-delay/) and [ep19](../ep19-smart-mailbox-letter-detector/) (the transistor latch) before this episode.

Every latch so far needed **two buttons**: one for ON and one for OFF. The goal here is a **one-button toggle**: press once → LED on and stays on, press again → LED off. Only transistors and basic parts are allowed, **no ICs**.

The episode has three parts:

1. A small **capacitor trick** that lets one point give either a "high" or a "low" signal.
2. Combining it with the latch to get the **one-press toggle**.
3. **Practical problems**: adding a relay breaks the circuit, a small capacitor makes it unreliable, and how each one is fixed.

---

## 1. Starting Point: ON and OFF on the Same Node

The instructor first shows a variation of the latch where the ON and OFF buttons are **both connected to the same node** (the input of the latch):

- **ON** connects that node to **+V** → the first transistor turns on → it sends **0 V** to the second transistor (a PNP) → the PNP turns on and feeds the first transistor back, so it stays on even after ON is released.
- **OFF** connects the **same node** to **0 V** → the first transistor turns off → the PNP and the LED turn off.

The nice thing about this version: the latch only cares about **what voltage reaches one node**. High = ON, low = OFF.

The bad thing: it still needs **two switches**.

> **Key question:** If one switch could connect that node to **something that is sometimes high and sometimes low**, one button would be enough.

---

## 2. The Hint Circuit: a Capacitor as a "Memory" Point

```
+9 V ── R ── R ──┬── node C ──► (to the push button)
                 │
                 C (capacitor)
                 │
                0 V
          (+ a discharge path across C: switch → later a transistor)
```

- **Charging:** The supply charges the capacitor through the resistors. The voltage at node C rises **slowly** toward +9 V. A bigger C or bigger R → slower.
- **Discharging:** Close a switch across the capacitor and it empties to ground. Node C drops to **≈ 0 V**.
- That switch can be replaced by a **transistor**, so the discharge can be triggered by an electronic signal instead of a finger.

So node C can be **high** (charged) or **low** (discharged), and it remembers that state for a while.

The capacitor charges along the usual RC curve:

$$
V_C(t) = V_{supply}\left(1 - e^{-t/RC}\right), \qquad \tau = R\,C
$$

After about $5\tau$ the capacitor is practically full.

**Circuit Wizard tip:** If the charging looks instant, open the **simulation timing control** and set the time step to about **1 ms**. Then you can watch the voltage climb step by step.

---

## 3. The Design: One-Press Toggle

**Idea:** Replace the separate ON and OFF switches with **one push button** between **node C** and the **latch input**.

- If node C is **high** when you press → the latch gets a high signal → **ON**.
- If node C is **low** when you press → the latch gets a low signal → **OFF**.

So the trick is to make node C **automatically** low while the latch is on, and high while it is off.

### Problem: the second press does nothing

Just connecting the button to node C isn't enough. In the video's simulation:

1. The capacitor charges to about **5 V** → press → LED **on** ✔.
2. Press again (and again) → **nothing happens**. Node C is still around **6–7 V** (the capacitor only lost a little charge and keeps charging), so every press is still an **ON** press.

### Fix: let the latch discharge the capacitor

Add a **discharge transistor** across the capacitor and drive its base **from the latch itself**:

- The base needs a point that is **clearly high when the latch is ON**. One point in the latch was only about **1.5 V**, so it wasn't chosen. Another point was about **8.85 V** when ON, so the discharge transistor's base was connected there.
- **Latch ON** → discharge transistor ON → it pulls node C to ground, and the current coming down through the charging resistors goes through it too. Node C drops to about **14 mV** (practically 0 V).
- **Next press** → 0 V reaches the latch input → **OFF** ✔.
- **Latch OFF** → discharge transistor OFF → the capacitor charges again → node C goes high → ready for the next ON.

| Latch state | Discharge transistor | Node C | Next press does |
|---|---|---|---|
| OFF | off | charges up (high) | **ON** |
| ON | on | ≈ 0 V (14 mV) | **OFF** |

**Timing:** You can't press too fast. After each press the capacitor needs time to **recharge** (after OFF) or **discharge** (after ON). Choose C and the resistors so it's ready quickly, but not so small that the circuit becomes unstable (see Section 5).

The big idea of the episode: **one simple capacitor, plus a transistor, turns one point into something that is sometimes positive and sometimes zero**, and that is enough to make a single button toggle.

---

## 4. Problem: Driving a Relay

To switch a real load (a fan, a motor), you'd want a **relay** instead of the LED, or in parallel with it.

### Attempt 1: relay coil in place of / in parallel with the LED ✘

- A relay coil is a **small resistance**.
- The current that should flow into the **feedback branch** (the branch that keeps the transistors on or off) goes into the coil instead.
- That branch gets almost **no current**, so its node sits near **0 V**.
- 0 V there is **lower** than the PNP's emitter by more than 0.7 V, so it looks like an ON signal. The circuit **switches itself on at power-up**, without anyone pressing the button.

> **Lesson:** Don't hang a low-resistance load directly on the latch's output. It steals the feedback current.

### Attempt 2: NPN transistor to drive the relay ✘

Next idea: drive the relay with an **NPN** transistor (relay on top, emitter to ground, about **10 kΩ** on the base).

Before running it, the instructor asks: **what will happen?**

- The latch's output voltage now feeds the NPN's base too. The NPN's base–emitter junction **clamps** that node to about **0.7 V**.
- 0.7 V is still far below the PNP's emitter (≈ 9 V), so again it looks like ON → the circuit turns on **by itself**.
- Once things start turning on, another node is pulled to ≈ 0 V and one transistor turns another off. The transistors end up **fighting** each other.

### Fix: use a PNP driver with the load at the bottom ✔

- Use a **PNP** transistor: **emitter to +V**, **load (relay) moved below it** to ground, about **1 kΩ** on the base.
- A PNP "high-side" switch like this always works with the load **underneath** it.
- Result in the simulation: power on → nothing turns on (correct ✔). Press → LED and load on ✔. Press → both off ✔.

**My explanation (not from the video):** the PNP's base–emitter junction sits up near +9 V, so it can't drag a latch node down to 0.7 V the way the grounded NPN did. It only pulls current when the latch is already ON.

Now other loads can run **in parallel with the LED** without upsetting the latch.

---

## 5. Hardware Build

The circuit was built on a board with roughly the same values as the simulation:

| Part | Value |
|---|---|
| Supply | 9 V |
| Resistors (visible on the board) | 100 kΩ, 10 kΩ |
| Capacitor | **220 µF** (first tested with **1 µF**) |
| Button | one push button for ON/OFF |
| Relay driver | PNP transistor + 1 kΩ base resistor (the part number sounds like 2N5401) |

### Capacitor value matters

- **1 µF:** the LED **flickers** when the button is pressed. Sometimes it toggles, sometimes it doesn't. Not reliable.
- **220 µF:** clean, stable ON / OFF every time. The bigger capacitor gives node C more time and a steadier voltage, so the transistor gets a clear ON or OFF signal.

Rough time constants, to compare:

$$
\tau = R C: \quad 10\ \text{k}\Omega \times 1\ \mu\text{F} = 10\ \text{ms} \qquad 10\ \text{k}\Omega \times 220\ \mu\text{F} = 2.2\ \text{s}
$$

**My guess at the reason:** with 1 µF, node C changes in a few milliseconds, which is about as fast as a mechanical button **bounces** (its contacts chatter for a few ms). So one press can look like several presses. With 220 µF, node C can't change that fast.

The simulation **didn't show** this problem. It only appeared on the real board.

### Relay tests on the board

| Version | Result |
|---|---|
| Relay directly in parallel with the LED | **Nothing works** at all ✘ |
| NPN driver, relay on top | Fan turns **on** but **won't turn off** ✘ (circuit stuck ON) |
| **PNP** driver, 1 kΩ, relay at the bottom | Fan **off / on** correctly and stable ✔ |

### Design philosophy

The instructor sometimes picks values **without calculating** on purpose. Even in large companies, not every value comes from pen-and-paper calculations. For transistor base resistors, anything in the **kilo-ohm range** (1 kΩ, 4.7 kΩ, 10 kΩ) usually works. Real parts don't match their nominal values exactly anyway (for example β), so testing and adjusting is part of the job.

---

## Summary of Values from the Video

| Item | Value |
|---|---|
| Supply | 9 V |
| Node C when charged (sim) | ≈ 5 V at the first press, 6–7 V later, up to ≈ 8–9 V |
| Discharge transistor base (latch ON) | ≈ 8.85 V |
| Node C when discharged (sim) | ≈ 14 mV |
| Capacitor | 1 µF (unstable) → **220 µF** (stable) |
| Resistors on the board | 100 kΩ, 10 kΩ |
| NPN relay driver base resistor | 10 kΩ (failed) |
| PNP relay driver base resistor | 1 kΩ (worked) |
| Base resistor rule of thumb | 1 kΩ – 10 kΩ |

---

## My Circuit Wizard Simulation

*Not done yet.* I'll add my simulation here (one-press toggle, then the relay versions).

---

## What I Learned

- A latch whose ON and OFF both connect to **one node** only needs that node to be **high or low**. A single button can do both if it connects to a point that changes.
- A **capacitor** can act as a short-term memory: charged = high, discharged = low.
- To toggle, the latch must **change that point itself**: when it turns on, it switches on a transistor that **discharges the capacitor**, so the next press becomes an OFF press.
- The discharge transistor's base must come from a node that is **clearly high when ON** (≈ 8.85 V), not a weak one (≈ 1.5 V).
- A **relay coil** has a low resistance and can **steal the feedback current** of a latch, making it turn on by itself.
- An **NPN driver** on the latch output clamps the node to ≈ 0.7 V and causes the same problem. A **PNP high-side driver** (emitter to +V, load at the bottom) fixes it.
- The simulation can hide real-world problems: a **1 µF** capacitor worked in software but was unstable on the board, and **220 µF** fixed it.
- Not every value needs exact calculation: kΩ-range base resistors usually work, and the final check is testing.

### Next to try

- Build the one-press toggle in Circuit Wizard and measure node C in both states (expect ≈ 8–9 V and ≈ 0 V).
- Find which latch node gives ≈ 8.85 V vs. ≈ 1.5 V, and try the discharge transistor on each.
- Hold the button down after an ON press: does the discharge transistor pull node C low and turn the latch straight back off?
- Press faster and faster to find the shortest time between presses that still works, for 1 µF and 220 µF.
- Recreate the three relay versions (coil in parallel, NPN driver, PNP driver) and confirm only the PNP one works.
