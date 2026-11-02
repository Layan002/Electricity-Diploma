# Ep16 – Designing a Joystick Circuit That Controls a Motor for a Toy Factory

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [تصميم دائرة لجويستيك (Joystick) يتحكم بمحرك لمصنع العاب (Designing a joystick circuit that controls a motor for a toy factory)](https://www.youtube.com/watch?v=5NAKRSh9CCM&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=154) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

You need to already know how a **relay** works and what its terminals do (**COM**, **NC** = normally closed, **NO** = normally open) before this episode.

**The task:** a toy factory owner wants a simple circuit that turns a **DC motor left or right** using a **joystick**.

- Only **relays** are allowed: no transistors, no diodes, no resistors.
- The joystick has **4 micro switches**, one at each edge. Pushing the stick presses one of them.
- Each micro switch has **only 2 terminals**: a plain on/off contact.
- We only need **2 of them**: one for **right** and one for **left**.

The episode builds up to the answer step by step: first with switches only, then with relays when the switches aren't good enough.

---

## 1. How to Reverse a DC Motor

A DC motor turns the other way when you **reverse its polarity**: swap which terminal gets **+** and which gets **−**.

$$V_{motor} = V_A - V_B$$

| $V_A$ | $V_B$ | $V_{motor}$ | Motor |
|---|---|---|---|
| +12 V | 0 V | +12 V | turns one way |
| 0 V | +12 V | −12 V | turns the other way |
| equal (both 0 V or both 12 V) | | 0 V | stopped |

So the design question is: **what part can swap + and − on the two motor terminals?**

---

## 2. First Idea: One DPDT Switch (No Relays)

A **double-pole double-throw (DPDT)** switch has two 3-terminal sections (COM, NC, NO) that move **together** with one press.

- Each motor terminal goes to one **COM**.
- On one section, NC = **+** and NO = **−**. On the other section it's crossed: NC = **−** and NO = **+**.
- Not pressed: terminal A gets +, terminal B gets − → turns one way.
- Pressed: both COMs move, A gets −, B gets + → turns the other way.

✔ It reverses the motor.
✘ There is **no stop position**. It's always turning one way or the other, so you'd need **another switch** to cut the motor off.

---

## 3. Second Idea: Two Separate 3-Terminal Switches (No Relays)

Split the DPDT into **two independent SPDT switches** (each with COM, NC, NO), wired the same way on both:

- **COM** → one motor terminal
- **NC** → **0 V** (battery −)
- **NO** → **+12 V**

| Switch 1 | Switch 2 | Terminal A | Terminal B | Motor |
|---|---|---|---|---|
| released | released | 0 V | 0 V | **stopped** |
| pressed | released | +12 V | 0 V | turns **left** |
| released | pressed | 0 V | +12 V | turns **right** |
| pressed | pressed | +12 V | +12 V | **stopped** |

✔ Left, right **and** stop, with no relays at all.

**The catch:** this needs switches with **3 terminals**. The joystick's micro switches only have **2** (a single on/off contact). If the joystick had 3-terminal switches, we wouldn't need relays.

---

## 4. The Solution: A Relay Replaces Each 3-Terminal Switch

A relay already has exactly the contact we're missing: **COM, NC and NO**. The only difference is that it's switched **electrically** (by powering its coil) instead of by pressing it.

So the joystick's 2-terminal switch is used only to **power the relay coil**, and the relay's contacts do the polarity switching. Pressing the joystick = "pressing" the 3-terminal switch.

### The final circuit

| Part | Connection |
|---|---|
| Relay 1 coil | one end → battery −, other end → joystick **right** switch → battery + |
| Relay 2 coil | one end → battery −, other end → joystick **left** switch → battery + |
| Relay 1 COM | motor terminal A |
| Relay 2 COM | motor terminal B |
| Both NC contacts | **+12 V** |
| Both NO contacts | **0 V** (battery −) |

### How it works

| Right (relay 1) | Left (relay 2) | Terminal A | Terminal B | Motor |
|---|---|---|---|---|
| off | off | +12 V (NC) | +12 V (NC) | **stopped** (no voltage difference) |
| on | off | 0 V (NO) | +12 V (NC) | turns **right** |
| off | on | +12 V (NC) | 0 V (NO) | turns **left** |
| on | on | 0 V | 0 V | **stopped** |

- At rest both motor terminals sit at **+12 V**. The motor doesn't turn, because it only cares about the **difference** between its terminals.
- Pressing one direction pulls **only that side** down to 0 V.
- Pushing both at once (not really possible with one stick, but good to check) gives 0 V on both sides, so the motor stops. **Nothing shorts** in any state.

### The other valid way

If you'd rather have **0 V on both sides at rest**, swap the contacts on both relays: **NC → 0 V** and **NO → +12 V**. The instructor says this is equally correct. Pressing a direction then lifts that side to +12 V instead of pulling it down. (This is the version I built myself in simulation 5 below.)

---

## 5. The Hardware Build

| Part | What was used |
|---|---|
| Supply | 12 V |
| Joystick | 4 micro switches, 2 terminals each. Only 2 used (right and left) |
| Relays | 2. The ones available were **double relays** (two contact sets on one coil), but only **one set** of each was used |
| Load | small DC motor |

- Both directions worked straight away with **only two relays**.
- A double relay isn't needed. Each relay only has to switch **one** COM/NC/NO set, and the two relays work at **different times**, so two plain single-contact relays (COM, NC, NO) are enough.
- With properly rated relays the same circuit can drive **high-current motors**, with none of the extra worries that transistors bring.
- **Limitation:** this circuit controls **direction only**. Speed control would need a transistor circuit.

**The main lesson** is the way of thinking: start with the simplest part (a switch that swaps polarity), see what the joystick can't give you (a third terminal), and use a relay to supply exactly that missing piece.

---

## My Circuit Wizard Simulations

All files are in [`media/`](./media/). The GIFs play on this page (cropped to the circuit). Click the MP4 links for the full recordings. In Circuit Wizard's voltage view, **red = high voltage** and **green = near 0 V**. When both motor wires are the same colour, there's no voltage across it. Times in the tables are from the MP4s.

I simulated **5 circuits**. Circuits 1, 3 and 4 are from the video. **Circuits 2 and 5 are my own designs**, and in circuit 5 I first wired one relay wrong and then fixed it.

| # | Circuit | From | Battery |
|---|---|---|---|
| 1 | DPDT switch reverses the motor | video (idea 1) | 12 V |
| 2 | Two SPDT switches, COMs on the battery side | **my design** | 12 V |
| 3 | Two SPDT switches, COMs on the motor | video (idea 2) | 12 V |
| 4 | Joystick relay circuit, NC → +V, NO → 0 V | video (final circuit) | 9 V |
| 5 | Relay circuit, NC → 0 V, NO → +V: wrong wiring, then corrected | **my design** | 9 V |

I used **9 V** for the relay circuits instead of the video's 12 V. That changes the voltage levels but not the logic.

📁 Circuit files:
- [ep16-joystick-switches.cwz](./ep16-joystick-switches.cwz): Sims 1–3 (3 × 12 V ideal battery, 3 × DC motor (3 V), 1 DPDT switch, 4 SPDT switches)
- [ep16-joystick-relays.cwz](./ep16-joystick-relays.cwz): Sims 4–5, all three relay circuits including the wrongly wired RL3/RL4 and the corrected RL5/RL6 (3 × 9 V ideal battery, 3 × DC motor (3 V), 6 × SPDT relay (6 V), 6 SPST switches)

**Part ratings:** the motors are Circuit Wizard's **3 V** DC motor and the relays are **6 V** relays, but they run on 12 V and 9 V here. That's fine for checking the logic in a simulation. A real build should use a motor and relay coils rated for the supply (12 V parts on a 12 V battery, as in the video).

---

### Sim 1 – DPDT switch (from the video)

![DPDT switch reversing the motor](media/ep16-sim-dpdt-switch-reversing.gif)

*Each click of SW3 swaps which motor wire is red (+12 V) and which is green (0 V), so MT2 reverses every time. Notice there's never a moment when both motor wires are the same colour: a DPDT alone can't stop the motor.* · [MP4](media/ep16-sim-dpdt-switch-reversing.mp4)

**Wiring:** I wired the DPDT the mirror way round from the video: the battery goes to the two **COMs** (+ on the top pole, − on the bottom pole) and the four throws are crossed to the motor. That works just as well for a DPDT, because both poles always move together.

| Time | SW3 position | Motor top | Motor bottom | MT2 |
|---|---|---|---|---|
| 0–4 s | (not simulating) | – | – | – |
| 5 s | up | +12 V | 0 V | turns one way |
| 7 s | down | 0 V | +12 V | reverses |
| 12 s | up | +12 V | 0 V | reverses again |
| 18 s | down | 0 V | +12 V | reverses again |

✔ Matches the video: it reverses, but there's no stop.

---

### Sim 2 – Two SPDT switches with the COMs on the battery (my design)

![Two SPDT switches with COMs on the battery side](media/ep16-sim-two-spdt-com-on-battery.gif)

*SW1's COM is on battery +, SW2's COM is on battery −. Watch for the moments when the **whole circuit turns green**: those are short circuits, where + and − land on the same motor terminal.* · [MP4](media/ep16-sim-two-spdt-com-on-battery.mp4)

**Idea:** split my DPDT from Sim 1 into two separate switches, keeping the battery on the COMs. SW1's two throws go to motor top / bottom, and SW2's two throws go to motor bottom / top.

| SW1 (+) | SW2 (−) | Motor top | Motor bottom | Result |
|---|---|---|---|---|
| up → top | up → bottom | +12 V | 0 V | turns one way ✔ |
| down → bottom | down → top | 0 V | +12 V | turns the other way ✔ |
| up → top | down → top | + and − **joined** | floating | **short circuit** ✘ |
| down → bottom | up → bottom | floating | + and − **joined** | **short circuit** ✘ |

**What happened in the recording:** reverse at ≈3 s, **short at ≈4–5 s** (everything green), forward at 5–9 s, **short again at ≈10 s**, reverse at 11–14 s.

**What I learned from it:** when the two halves of a DPDT become **independent** switches, it matters where the COMs go. With the battery on the COMs, each switch only chooses **which motor terminal** gets its + or −, and nothing stops both from choosing the same terminal. That's a dead short across the battery, and there's still no stop position. Putting the COMs on the **motor** instead (Sim 3) means each motor terminal chooses between + and −, so a short is impossible and "both the same" means stop. A real battery would get very hot (or a fuse would blow) in the short states, so this version should only be used with switches that are mechanically tied together, which turns it back into a DPDT.

---

### Sim 3 – Two SPDT switches with the COMs on the motor (from the video)

![Two SPDT switches with COMs on the motor](media/ep16-sim-two-spdt-com-on-motor.gif)

*Each switch connects one motor terminal to either + or −. When SW4 and SW5 are both on the − side, both motor wires turn green and MT3 stops.* · [MP4](media/ep16-sim-two-spdt-com-on-motor.mp4)

**Wiring:** SW4 COM → motor top, SW5 COM → motor bottom. On each switch one throw goes to battery + and the other to battery −.

| Time | SW4 (motor top) | SW5 (motor bottom) | MT3 |
|---|---|---|---|
| 0–10 s | (not simulating) | – | – |
| 11–13 s | + | − | turns one way |
| 14 s | − | − | **stopped** |
| 15–20 s | − | + | turns the other way |
| 21–22 s | − | − | **stopped** |
| 23–26 s | + | − | turns the first way again |

✔ Matches the video's truth table: left, right and stop, and no short in any position.

---

### Sim 4 – The joystick relay circuit (from the video)

![Joystick relay circuit with NC to +V](media/ep16-sim-relay-joystick-nc-positive.gif)

*SW1 and SW2 stand in for the joystick's two micro switches. At rest both motor wires are red (+9 V through the NC contacts) and MT1 is still. Closing SW1 or SW2 flips one relay and turns just that side green, so the motor runs; with both closed, both sides are green and it stops.* · [MP4](media/ep16-sim-relay-joystick-nc-positive.mp4)

| Part | Connection |
|---|---|
| B1 = 9 V battery | supply |
| SW1, SW2 = SPST switches | the joystick's right and left micro switches. Each one feeds a relay coil from B1 + |
| RL1, RL2 = SPDT relays | coils: other end to B1 −. **COM** → motor (RL1 → bottom, RL2 → top), **NC** → +9 V, **NO** → 0 V |
| MT1 | DC motor between the two COMs |

| Time | SW1 (RL1) | SW2 (RL2) | Motor top | Motor bottom | MT1 |
|---|---|---|---|---|---|
| 2 s | off | off | +9 V | +9 V | stopped ✔ |
| 4–8 s | **on** | off | +9 V | 0 V | turns one way ✔ |
| 10–12 s | on | **on** | 0 V | 0 V | stopped ✔ |
| 14–18 s | off | **on** | 0 V | +9 V | turns the other way ✔ |
| 20 s | off | off | +9 V | +9 V | stopped ✔ |
| 22 s | **on** | off | +9 V | 0 V | one way ✔ |
| 26–28 s | off | **on** | 0 V | +9 V | other way ✔ |
| 30 s | off | off | +9 V | +9 V | stopped ✔ |

✔ All four states match the truth table in section 4.

---

### Sim 5 – Relay circuit with NC → 0 V (my design): wrong wiring, then corrected

![Relay circuit with NC to 0 V, first wired wrong and then corrected](media/ep16-sim-relay-nc-ground-wrong-and-fixed.gif)

*The top circuit (RL3/RL4) is my first try. Watch what happens when SW4 is closed: the **whole circuit goes green** because RL4 shorts the battery, and MT2 doesn't move. The bottom circuit (RL5/RL6) is the corrected version, where each switch runs the motor and both together stop it. At the end the view zooms out to show both circuits side by side.* · [MP4](media/ep16-sim-relay-nc-ground-wrong-and-fixed.mp4)

**Idea:** build the "other valid way" from section 4, which the instructor only described: **0 V on both motor terminals at rest** (NC → 0 V) and **+9 V on a side when its relay is on** (NO → +9 V).

#### First try: RL3 / RL4 (one relay wired wrong)

| Relay | COM | NC | NO | Correct? |
|---|---|---|---|---|
| RL3 (SW3) | motor top | 0 V | +9 V | ✔ |
| RL4 (SW4) | **0 V** | **motor bottom** | +9 V | ✘ COM and NC swapped |

| Time | SW3 | SW4 | What happened |
|---|---|---|---|
| 0–2 s | – | – | not simulating |
| 4–6 s | on | off | motor top +9 V, bottom 0 V (through RL4's NC → COM → 0 V), MT2 runs ✔ |
| 8 s | off | off | both sides 0 V, stopped ✔ |
| 9–11 s | off | **on** | RL4's COM moves to NO, which connects **+9 V straight to 0 V** through the contact. That's a **short circuit**: the whole circuit drops to 0 V (all green). The motor bottom is left on the NC contact, connected to nothing, so MT2 can't run ✘ |

So with RL4 wired like that, the motor could only turn **one way**, and the other direction shorted the battery. A relay's **COM is the moving contact**, so it has to go to the thing being switched (the motor terminal), not to a supply rail.

#### My correction: RL5 / RL6

**I edited the connection** so both relays are wired the same way, with the motor on the COMs:

| Relay | COM | NC | NO |
|---|---|---|---|
| RL5 (SW5) | motor top | 0 V | +9 V |
| RL6 (SW6) | motor bottom | 0 V | +9 V |

| Time | SW5 (RL5) | SW6 (RL6) | Motor top | Motor bottom | MT3 |
|---|---|---|---|---|---|
| 14–18 s | off | **on** | 0 V | +9 V | turns one way ✔ |
| 20 s | off | off | 0 V | 0 V | stopped ✔ |
| 22 s | **on** | off | +9 V | 0 V | turns the other way ✔ |
| 24–25 s | **on** | **on** | +9 V | +9 V | stopped ✔ |
| 27 s | off | **on** | 0 V | +9 V | one way ✔ |
| 28–30 s | on | on | +9 V | +9 V | stopped ✔ |
| 31–45 s | | | | | zoomed out: the wrong circuit is tested again (SW4 still shorts it) next to the corrected one, which still works |

✔ After the fix, this version behaves exactly like Sim 4, just "upside down": the motor rests at 0 V on both sides instead of +9 V, and nothing shorts in any state.

---

## What I Learned

- A DC motor reverses when you **swap + and −** on its terminals. It stops whenever both terminals are at the **same** voltage, whether that's 0 V or the full supply.
- A **DPDT** switch reverses a motor but has no stop position.
- Two **independent** 3-terminal switches give left, right and stop, **but only if the COMs go to the motor**. **From my Sim 2:** with the COMs on the battery, two of the four switch positions short the battery.
- The joystick's micro switches only have 2 terminals, so a **relay** supplies the missing COM/NC/NO contact. The joystick just powers the coil.
- **From my Sim 5:** the relay's **COM is the moving contact** and must go to the motor terminal. When I swapped COM and NC on one relay, that direction shorted the battery instead of running the motor. After I corrected the wiring, the circuit worked.
- Both relay versions are valid: **NC → +V, NO → 0 V** (motor rests at +V on both sides) or **NC → 0 V, NO → +V** (motor rests at 0 V on both sides).
- Relays only give **direction** control. Speed control needs transistors.

### Next to try

- Add an **ammeter** in series with the battery in Sim 2 and the first try of Sim 5 to see how big the short-circuit current is (a real battery would need a fuse).
- Use push-to-make switches instead of latching SPST switches for SW1/SW2, so they behave like a real joystick that springs back.
- Add a **flyback diode** across each relay coil in a real build (outside this episode's "relays only" rule), to protect the joystick contacts when the coil switches off.
- Swap in a motor and relays rated for the supply voltage and check the circuit still behaves the same.
