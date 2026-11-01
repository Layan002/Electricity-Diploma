# Ep15 – Designing an Interactive Smart Board for Schools Without Programming

**Course:** Professional Electronic Design Course – Level 2 (Advanced Electronic Design)
**Source:** Waleed Issa Academy – [تصميم لوحة ذكية تفاعلية للمدارس بدون برمجة (Designing an interactive smart board for schools without programming)](https://www.youtube.com/watch?v=3vsy5e0FXe0&list=PLww54WQ2wa5qVh1p8iPi7HspX7N9hbvbc&index=153) (video in Arabic)
**Tool:** Circuit Wizard

> This is my own English summary of the episode, written for study purposes. All credit for the content goes to Waleed Issa Academy.

---

## Overview

This episode uses **no electronic parts at all**: no resistors, transistors or ICs, and no programming. The whole design is made of **switches and contacts**, a battery and a lamp. The point is that wiring alone can make a circuit "smart".

**The task:** a teacher wants a **question-and-answer board** for the classroom:

- **4 question switches** (Q1–Q4). The teacher turns on one of them to say "answer this question now".
- **4 answer cards**, each with its own **answer switch** (A1–A4) under it. A student presses the switch under the card they think is right.
- **1 lamp** (a ✔ sign) and a **12 V battery**. The lamp lights **only** when the active question's switch and the switch of its **correct answer** are both closed.

**The smart part:** the teacher must be able to **move the answer cards around** (swap their places), and the board must still know where each correct answer is. Otherwise students just memorise "question 1 is always the top answer".

Only switches or **contacts** are allowed (two pieces of metal that touch also count as a switch).

---

## 1. First Try: Fixed Wiring (Works, but Not Smart)

The lamp's negative side goes straight to the battery's negative terminal. It needs **+12 V** on its other side, and that can only come through the switches.

Wire each question switch in **series** with the switch of its answer, and join the outputs of all the answer switches to the lamp:

$$+12\ \text{V} \rightarrow Q_1 \rightarrow A_1 \rightarrow \text{lamp} \rightarrow 0\ \text{V}$$

and the same for $Q_2 \rightarrow A_2$, $Q_3 \rightarrow A_3$, $Q_4 \rightarrow A_4$.

| Test | Result |
|---|---|
| Q1 on + A1 pressed | lamp on ✔ |
| Q1 on + A2 pressed | lamp off ✔ (A2 only gets voltage from Q2) |
| Q2 on + A2 pressed | lamp on ✔ |
| Teacher swaps cards 2 and 3 | ✘ the wires behind the board don't move, so the "correct" switch is still the old position |

The series connection is an **AND** (the question **and** its answer), and joining all the outputs at the lamp is an **OR** (any matching pair lights it). That's correct logic, but the answer positions are fixed by the wiring, so it isn't smart.

---

## 2. The Smart Design: Each Card Tells the Board Who It Belongs To

Moving a card has to **change something electrically**. The idea:

- Behind **every card position (slot)** there are **4 contacts**, one per question: contact 1, contact 2, contact 3, contact 4.
- Each **answer card** is made so it closes **only one** of those contacts: the card for question 1 closes contact 1, the card for question 2 closes contact 2, and so on.
- Wherever you put the card, it closes "its" contact in **that** slot.

### How it's wired

1. **Question switches feed contact columns.** $Q_1$ sends +12 V to contact 1 in **all four slots**. $Q_2$ feeds contact 2 in all four slots, etc.
2. **Each slot's contacts are joined** and go to that slot's **answer switch**.
3. All answer switches go to the **lamp**, and the lamp goes to 0 V.

So with $Q_n$ on, +12 V reaches only the slot whose card closes contact $n$, which is the slot holding the card for question $n$. Only that slot's answer switch has voltage on it, so only that switch can light the lamp.

If the card moves to another slot, it closes contact $n$ there instead, and the "live" answer switch moves with it. No rewiring needed.

### The logic

Call $Q_q$ the question switch, $C_{s,q}$ the contact for question $q$ in slot $s$, and $A_s$ the answer switch under slot $s$ (1 = closed). The lamp is on when:

$$L = \sum_{q=1}^{4}\ \sum_{s=1}^{4} Q_q \cdot C_{s,q} \cdot A_s$$

where $\cdot$ means AND (switches in series) and $\sum$ means OR (paths in parallel). With only one question on, this is simply "the answer switch under the card that belongs to the active question".

**Number of contacts:** with $N$ questions and $N$ card slots you need $N \times N$ contacts. 4 questions → **16 contacts**, 10 questions → 100 contacts. The circuit idea is the same at any size.

### Walk-through example

Cards placed: slot 1 = card 4, slot 2 = card 1, slot 3 = card 2, slot 4 = card 3. The teacher turns on **Q3**:

| Slot | Card | Contact 3 closed? | Answer switch has +12 V? | Pressing it |
|---|---|---|---|---|
| 1 | card 4 | no | no | nothing |
| 2 | card 1 | no | no | nothing |
| 3 | card 2 | no | no | nothing |
| 4 | card 3 | **yes** | **yes** | **lamp on ✔** |

---

## 3. The Hardware Build

| Part | What was used |
|---|---|
| Supply | 12 V battery |
| Indicator | 1 lamp (the ✔ sign) |
| Question switches | 4 on/off switches (Q1–Q4) |
| Answer switches | 4 switches, one under each card slot |
| Contacts | 4 per slot (16 total): **brass paper fasteners** from a stationery shop, pushed through a cardboard backing |
| Card closer | a **magnet** on the back of each answer card that bridges a pair of fastener heads |

- The questions themselves are just **lightly glued** on. There's no circuit behind them, so they can be changed freely.
- The secret is all in the **switches** and the **contacts behind the answer cards**. The magnet's position on each card decides which contact (1, 2, 3 or 4) it closes. Placing the card bridges that contact in the slot, which tells the board "I'm the answer to question $n$".
- On the back, the four contacts in each slot are wired exactly like the Circuit Wizard drawing.

**Test on the board:** Q1 on → only the switch under card 1 lights the lamp. The teacher moves card 1 to slot 4 → the old position no longer works and slot 4 now lights the lamp. The same works for Q2 after moving its card.

### Limitations and improvements

- **Only one question at a time.** If two question switches are on, two contact columns are live and two different answers will light the lamp. Turn the old question off before choosing a new one.
- Add **one lamp per question** to show which question is active.
- You could also add a lamp per answer, but one lamp per question is the preferred upgrade.
- You can design a PCB for it or build it any other way. The circuit stays the same, and it scales to 10 questions or more.

---

## My Circuit Wizard Simulation

All files are in [`media/`](./media/). The GIF plays on this page (cropped to the circuit; card setup at 4×, testing at 2×). Click the MP4 link for the full 2½-minute recording.

![Smart quiz board simulation](media/ep15-sim-smart-quiz-board.gif)

*The 4 × 4 grid on the left is the 16 card contacts, SW21–SW24 are the answer switches, and SW1–SW4 on the right are the question switches. Watch which row turns red when a question is switched on: that's the slot holding its card, and only the answer switch on that row lights BL1. Near the end, card 2 is moved from slot 3 to slot 1, and the lamp follows it.* · [MP4](media/ep15-sim-smart-quiz-board.mp4)

### How my circuit is wired

| Part | Connection |
|---|---|
| B1 = 9 V | + to the four question switches, − to the lamp |
| SW1–SW4 | question switches Q1–Q4. Each one feeds one contact column |
| SW5–SW20 | the 16 card contacts in 4 rows (slots) × 4 columns (questions) |
| SW21–SW24 | answer switches for slots 1–4. Each one joins its row to the lamp line |
| BL1 | lamp, between the joined answer-switch outputs and B1 − |

Contact map (closing the contact = placing that question's card in the slot):

| Slot (row) | Answer switch | Contact for Q1 | Q2 | Q3 | Q4 |
|---|---|---|---|---|---|
| 1 | SW21 | SW5 | SW6 | SW7 | SW8 |
| 2 | SW22 | SW9 | SW10 | SW11 | SW12 |
| 3 | SW23 | SW13 | SW14 | SW15 | SW16 |
| 4 | SW24 | SW17 | SW18 | SW19 | SW20 |

I used a **9 V** battery instead of the video's 12 V. That doesn't change the logic, because there's nothing in the circuit except switches and the lamp.

### What happens in the recording

In Circuit Wizard's voltage view, **red = high voltage** and **green = near 0 V**. Times are from the original MP4.

| Time | Contacts closed (cards) | Question on | Answer pressed | Lamp | Matches the design? |
|---|---|---|---|---|---|
| 0–45 s | setting up: SW8 (card 4 in slot 1), SW10, SW13, SW14, SW19 | – | – | off | – |
| 54–61 s | same | Q4 (SW4) | SW22, SW23 | off | ✔ wrong slots |
| 70 s | same | Q4 | **SW21** (slot 1) | **on** | ✔ |
| 89 s | same | Q2 (SW2) | **SW22** (slot 2) | **on** | ✘ see below |
| 90.5 s | same | Q2 | **SW23** (slot 3) | **on** | ✔ |
| ≈ 94 s | fixed: SW8, SW9, SW14, SW19 → slots hold cards 4, 1, 2, 3 | Q2 | – | off | – |
| 103–108 s | same | Q2 | SW22, SW21, SW24 | off | ✔ wrong slots |
| 109 s | same | Q2 | **SW23** (slot 3) | **on** | ✔ |
| 111–125 s | card 2 moved from slot 3 to slot 1 (SW14 off, SW6 on), card 4 taken out of slot 1 (SW8 off) | Q2 | – | off | – |
| 132 s | SW6, SW9, SW19 | Q2 | **SW21** (slot 1) | **on** | ✔ answer followed the card |
| 135–140 s | same | Q2 | SW22, SW23 (the old slot), SW24 | off | ✔ |

**The mistake at 89 s.** In my first setup, slot 2 had **SW10** closed (a Q2 contact) and slot 3 had **both SW13 and SW14** closed (Q1 and Q2 contacts). So with Q2 on, **rows 2 and 3 both turned red**, and two different answers lit the lamp. The two closed contacts in slot 3 also carried Q2's voltage back into the **Q1 column** (SW5's node turned red even though SW1 was off). That's a sneak path: a card that bridges two contacts connects two questions together.

After I fixed it (one contact per card, each question's contact in only one slot), only the correct slot lit the lamp, and it kept following card 2 when I moved it.

---

## What I Learned

- **Series = AND, parallel = OR.** A question switch in series with an answer switch is "question AND answer". All the answer switches joined at the lamp is "any correct pair".
- Fixed wiring can check answers, but it can't handle **moving cards**. To make it smart, the card itself has to carry the information: it closes **its own contact** in whichever slot it sits in.
- With $N$ questions and $N$ slots you need $N^2$ contacts, wired as a grid: **columns = questions** (fed by the question switches) and **rows = slots** (going to the answer switches).
- **From my simulation:** every card must close **exactly one** contact, and each question's contact must be closed in **only one** slot. Otherwise two answers light up and voltage leaks into other questions' columns.
- Only **one question** can be on at a time, for the same reason.
- "Smart" behaviour doesn't always need programming or even transistors. A good wiring idea can be enough.

### Next to try

- Add a lamp on each question line (Q1–Q4) to show which question is active.
- Add an ammeter in series with BL1 to see the lamp current when it's lit.
- Replace the four question switches with one **rotary selector switch** so only one question can be on at once.
- Scale up to more questions and check that the grid still works with $N^2$ contacts.
