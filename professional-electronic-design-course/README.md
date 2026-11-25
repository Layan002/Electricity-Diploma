# Professional Electronic Design Course

My notes, summaries, and Circuit Wizard simulations for the **Professional Electronic Design Course** by Waleed Issa Academy (videos in Arabic).

**Tool:** Circuit Wizard

## Course Levels

The course is split into **6 levels**. Each level builds on the one before it, so the episodes are grouped by level below.

| Level | Name | Episodes | Status |
|---|---|---|---|
| 1 | Simple Electronic Design (التصميم الالكتروني البسيط) | 01 – 13 | ✅ Finished |
| 2 | Advanced Electronic Design (التصميم الالكتروني المتقدم) | 14 – 25 | 🟡 All 12 watched, simulations to do |
| 3 | Special "Block" Circuits (الدوائر الخاصة الصندوقية) | 26 – 37 | 🟡 In progress (8 / 12 watched) |
| 4 | — | 38 – 51 | ⬜ |
| 5 | — | 52 – 66 | ⬜ |
| 6 | PCB Design | 67 – 77 | ⬜ |

Ep26 opens Level 3 (special "block" circuits), ep38 opens Level 4 (starting with the 555 timer), ep52 opens Level 5, and ep67 opens Level 6 (the PCB course). I'll fill in the names of Levels 4 and 5 when I get there.

**Jump to:** [Level 1](#level-1--simple-electronic-design) · [Level 2](#level-2--advanced-electronic-design) · [Level 3](#level-3--special-block-circuits) · [Level 4](#level-4) · [Level 5](#level-5) · [Level 6](#level-6--pcb-design)

---

## Level 1 – Simple Electronic Design

Episodes 01–13. Basic transistor switching, LEDs, diodes and Zeners, voltage dividers, sensors (LDR, thermistor), RC delays and motor direction control, each ending in a small design task.

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 03 | Designing a simple circuit (closed / open) with full calculations | — (not uploaded yet) | ✅ Finished with simulation, not on GitHub yet |
| 04 | Designing a quiz board for two school teams | — (not uploaded yet) | ✅ Finished with simulation, not on GitHub yet |
| 05 | Designing a large LED display board and choosing its component values | — (not uploaded yet) | ✅ Finished with simulation, not on GitHub yet |
| 06 | Designing a relay circuit for a contest where the fastest team wins | — (not uploaded yet) | ✅ Finished with simulation, not on GitHub yet |
| 07 | Designing a fish tank circuit based on sunlight intensity, with a light cell | — (not uploaded yet) | ✅ Finished with simulation, not on GitHub yet |
| 08 | Push-pull pair, current-sensing transistor, light bulb tester design | [ep08-transistor-circuits-light-bulb-factory](./ep08-transistor-circuits-light-bulb-factory/) | ✅ Summary + simulations + videos + circuit files |
| 09 | Motor switch monitor with red/green LEDs: LED as voltage clamp, 3-diode, Zener and transistor-short designs | [ep09-motor-switch-monitor-leds](./ep09-motor-switch-monitor-leds/) | ✅ Summary + simulations of all 3 designs (incl. shared-resistor fault) + videos + circuit files |
| 10 | Battery charge status monitor: inverting transistor pair + 10 V Zener (green ≥ 10 V, red < 10 V), resistor design from β and $I_{Z,min}$ | [ep10-battery-charge-status-monitor](./ep10-battery-charge-status-monitor/) | ✅ Summary + simulation (voltage sweep, 10 kΩ vs 677 Ω) + video + circuit files |
| 11 | Car cooling fan: why saturation matters, NPN above a grounded load fails, NPN→PNP driver transistor, NTC thermistor divider ($R_2$ = 100 Ω) | [ep11-car-cooling-fan-control](./ep11-car-cooling-fan-control/) | ✅ Summary + 6 simulations (NPN above load, TIP127 driver, PNP→NPN, thermistor fan) + videos + circuit files |
| 12 | Car fan delayed turn-off: RC time delays (delayed on / off), diode & Zener thresholds, staircase-light timer, blocking diode on the thermistor fan circuit | [ep12-car-fan-delayed-turn-off](./ep12-car-fan-delayed-turn-off/) | ✅ Summary + 2 simulations (with vs. without blocking diode) + videos + circuit files |
| 13 | Useless machine: ground as a 0 V reference, ± supply from two batteries, NPN/PNP pair reverses a motor, NC limit switch in the −V line stops the return | [ep13-useless-machine-motor-circuit](./ep13-useless-machine-motor-circuit/) | ✅ Summary + 3 simulations (ground reference, ground position vs. Zener LED, useless machine with limit switch) + videos + circuit files |

---

## Level 2 – Advanced Electronic Design

Episodes 14–25. Circuits that make decisions on their own (codes, latches, oscillators) using only basic parts, without programming.

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 14 | Secret-code lock without programming: switches in series (AND), wrong switches pull the MOSFET gate to 0 V, 10 kΩ series resistor prevents the battery short, gate pull-down, BJT vs. MOSFET (speed, power loss, drive voltage) | [ep14-secret-code-circuit-no-programming](./ep14-secret-code-circuit-no-programming/) | ✅ Summary + simulation (code on, code broken, code + wrong switch without a short) + video + circuit file |
| 15 | Smart quiz board without programming: question switch AND answer switch, 4 × 4 contact grid (columns = questions, rows = card slots) so the correct answer follows its card, one contact per card | [ep15-interactive-smart-board-for-schools](./ep15-interactive-smart-board-for-schools/) | ✅ Summary + simulation (answer follows a moved card, double-contact mistake and fix) + video + circuit file |
| 16 | Joystick motor control with relays only: reversing polarity, DPDT (no stop), two 3-terminal switches with COMs on the motor, relay replaces the missing third terminal (NC → +V / NO → 0 V or the reverse) | [ep16-joystick-motor-control-toy-factory](./ep16-joystick-motor-control-toy-factory/) | ✅ Summary + 5 simulations (DPDT, 2 switch layouts incl. my COM-on-battery design with shorts, video relay circuit, my NC → 0 V relay circuit: wrong wiring + fix) + videos + circuit files |
| 17 | One-press drill with a relay latch: push-to-make ON, relay's own NO contact in parallel holds the coil on, push-to-break OFF before the split, DPDT (contact 1 latches, contact 2 switches the load), same circuit for 12 V DC or 220 V AC loads | [ep17-one-press-drill-relay-latch](./ep17-one-press-drill-relay-latch/) | ✅ Summary + 2 simulations (12 V motor, AC lamp) + videos + circuit file |
| 18 | Transistor latch with a delay: each collector feeds the other's base, NPN–NPN latch (ON/OFF ground a base, faint-glow fix with 1 kΩ across the LED), NPN + PNP latches (load at bottom for vehicles / at top), RC delay on the ON signal (100 kΩ, 470 µF ≈ 4 s), OFF moved across the capacitor | [ep18-transistor-latch-with-delay](./ep18-transistor-latch-with-delay/) | ✅ Summary + simulation (latch with ≈ 4–5 s turn-on delay, OFF across the capacitor) + video + circuit file |
| 19 | Smart mailbox: spotting latches in online circuits (feedback resistor, SET/RESET, same vs. mixed transistor types), laser + LDR divider (100 kΩ, ≈ 0 V lit / ≈ 8 V dark) drives a transistor that replaces the ON button, MOSFET version (1 MΩ gate pull-down), thyristor as a built-in latch | [ep19-smart-mailbox-letter-detector](./ep19-smart-mailbox-letter-detector/) | ✅ Summary + simulation (LDR-triggered latch, trigger ≈ 20 kΩ / 1.5 V, reset blocked while beam is dark) + video + circuit file |
| 20 | One-press ON/OFF toggle: ON and OFF on the same latch node, capacitor node as a high/low "memory", latch-driven discharge transistor makes the next press an OFF, relay coil or NPN driver makes the latch turn on by itself → PNP high-side driver (1 kΩ, load at the bottom), 1 µF unstable → 220 µF stable | [ep20-one-press-on-off-circuit](./ep20-one-press-on-off-circuit/) | 🟡 Summary only, simulation to do |
| 21 | Analysing three real circuits: car courtesy light with delayed turn-off (2.2 MΩ ÷ 2 divider, Q3 fires at 1.4 V on C), 3-LED battery indicator (Zener thresholds, NPN/PNP shorting an LED, 10 V → 11 V Zener), solar engine (4700 µF, 2.7 V Zener triggers at ≈ 3.5 V, NPN–PNP latch) | [ep21-analysing-three-transistor-circuits](./ep21-analysing-three-transistor-circuits/) | 🟡 Summary only, simulation to do |
| 22 | Flip-flop capacitor as an oscillator: capacitor voltage is relative ($V_{top} = V_{bottom} + V_C$), one plate held at 0.7 V → charge flips (+0.7 → −0.3 V), NPN + PNP one-capacitor flasher (9 V, 100 kΩ, 27 Ω, 33 µF ≈ 1.4 Hz, short ON / long OFF), speed-up capacitor for low supply, UPS low-battery flasher at 8 V (NPN across the capacitor, 100 kΩ / 6.5 kΩ divider or trimmer), latch (R + R) vs. oscillator (R + C) | [ep22-flip-flop-capacitor-oscillator-status-indicator](./ep22-flip-flop-capacitor-oscillator-status-indicator/) | 🟡 Summary only, simulation to do |
| 23 | Random-selection circuit with the oscillator: flip-flop capacitor demo (≈ 8 V → ≈ −7 V, recovers at 0.7 V), two-transistor flasher (2N2222, 470 Ω, 10 kΩ, 33 µF / 1 µF ≈ 9.67 Hz), R and C set ON/OFF times, LDR buzzer / speaker across collectors / toy organ, switch in the charging supply + 46 kΩ latch resistors hold the last LED | [ep23-random-selection-circuit-oscillator](./ep23-random-selection-circuit-oscillator/) | 🟡 Summary only, simulation to do |
| 24 | Boost (DC-to-DC) converter for a torch: inductor opposes current changes and adds to the battery when cut, inductor + switch + diode + capacitor, transistor driven by the ep23 oscillator (100 nF for high frequency), 3.7 V Li cell → ≈ 16 V for 6 (even 12) white LEDs in series (IRFZ44, 10 kΩ gate pull-down, 2.2 mH), load current limits the boost, two-transistor online circuit with steady brightness down to 3 V | [ep24-boost-circuit-efficient-torch](./ep24-boost-circuit-efficient-torch/) | 🟡 Summary only, simulation to do |
| 25 | Sequential oscillator (LED chaser) for decorative lights: ep23 flasher block repeated in a ring (collector → C → next base, last → first), LED in parallel with the transistor makes the ON move instead of the OFF, each capacitor sets the next step's time (220 µF → 33 µF), one NPN driver per colour (5 red / 5 green / 5 yellow, 50 mA, β ≈ 200, $R_B$ ≈ 25 kΩ → 22 kΩ), driver in linear mode (≈ 7 V across it), PNP driver inverts the pattern | [ep25-sequential-oscillator-decorative-lights](./ep25-sequential-oscillator-decorative-lights/) | 🟡 Summary only, simulation to do |

---

## Level 3 – Special "Block" Circuits

Episodes 26–37. Reading big schematics as blocks with a known shape and function, built from transistors: followers and regulators, protection, current sources and mirrors, amplifiers and oscillators.

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 26 | Introduction to Level 3: circuits as black-box "blocks" (shape + function), reading ≈ 80 – 90 % of a big schematic, two circuit families (decision-making ON/OFF vs. signal processing; remote control uses both), work plan: record each block with a one-sentence function, no mathematical proofs | [ep26-introduction-to-level-3-work-plan](./ep26-introduction-to-level-3-work-plan/) | ✅ Summary (intro episode, no circuit) |
| 27 | Emitter follower → 5 V linear regulator: follower as a buffer (high input R, current boost), divider collapses under load (9 V, 1 k / 1.25 k → 1.3 V at 200 Ω), 5.6 V Zener on the base holds 5 V for 9 – 30 V in, 2N2222 fails at 0.5 A, low-β 2N3055 loads the Zener (7.8 mA), Darlington (6.8 V Zener, 2 × V_BE), complementary Darlington 2N2222 + MJ2955 (1.1 A at ≈ 5 V, 38 µA base), push-pull block | [ep27-emitter-follower-special-circuits](./ep27-emitter-follower-special-circuits/) | 🟡 Summary only, simulation to do |
| 28 | Conventional power supply with protection blocks: op-amp feedback (2.5 V ref, 10 k / 10 k → 5 V), op-amp + follower with $V_{BE}$ inside the loop, current limiter ($I \approx 0.7/R_{SC}$), SCR + Zener crowbar with fuse, foldback limiting (5 V / 2.5 A → 1.7 A into a short), BJTs in parallel (thermal runaway, emitter resistors) vs. MOSFETs, blocks inside the LM317 and 7805 | [ep28-conventional-power-supply-current-limiters](./ep28-conventional-power-supply-current-limiters/) | 🟡 Summary only, simulation to do |
| 29 | Unusual uses of the transistor: diode-connected transistor (base on collector), 0.7 V lift against crossover distortion and for a zero-offset PNP→NPN follower (10 V in → 10 V out), matched pair for temperature compensation, diode + small resistor → reference → constant current, $V_{BE}$ multiplier $V_{BE}(1 + R_1/R_2)$, reverse E–B junction as a ≈ 6 V Zener (BC547, ruins β), multi-emitter area ratio (10×) in the LM35 (10 mV/°C) | [ep29-unusual-uses-of-the-transistor](./ep29-unusual-uses-of-the-transistor/) | 🟡 Summary only, simulation to do |
| 30 | Window (range) circuits → Schmitt trigger: one-threshold switch chatters with noise, input switches at $V_E + 0.7$, two NPNs sharing an emitter resistor (6 V demo: 100 Ω → ≈ 1.5 – 3 V window; 10 Ω narrow, 300 Ω wide), heater controller without an IC (NTC ≈ 6 kΩ @ 40 °C / 2.3 kΩ @ 60 °C, 9 V, 2N2222 × 2 + PNP + relay, $I_E$ ≈ 17.3 mA, $R_E$ = 100 Ω → ≈ 2.5 V pivot, divider 1 kΩ, 40 mW check), hardware test | [ep30-window-circuits-heater-temperature-control](./ep30-window-circuits-heater-temperature-control/) | 🟡 Summary only, simulation to do |
| 31 | Short (single) pulse generator: C in series + R to a rail gives one spike of width ≈ $RC$ (R to ground vs. to +V flips it), transistor after the RC inverts / stretches the pulse (pulse extender, base resistor for BJTs), two-transistor analysis (4.4 V on C → Q2 base jumps to −4.4 V, output high until it recharges to 0.6 V), holding transistor so a very short trigger gives a full pulse, CD4017 counter starting on a random LED fixed with a power-on reset (100 nF +5 V → RESET, 10 kΩ to ground) | [ep31-short-pulse-generator](./ep31-short-pulse-generator/) | 🟡 Summary only, simulation to do |
| 32 | Constant current source: resistor alone fails (9 V, 150 Ω: 3 / 2 / 1 LEDs → 20 / 33 / 46 mA), fixed voltage across a fixed resistor via the base ($I = (V_B - V_{BE})/R$), references (diodes, Zener, 7805, diode-connected transistor, $V_{BE}$ multiplier, op-amp), source vs. sink, examples (5.6 V Zener / 10 kΩ → 0.5 mA, 3 diodes + PNP / 560 Ω → 2 mA), 7805 ($5/R$) and LM317 ($1.25/R$), arrow symbols in schematics, constant-current capacitor ramp, two-mode torch (5 / 10 LEDs, PNP + diode + 2.7 V Zener, 1 kΩ, 100 Ω → 25 mA both modes, measured 25 – 26 mA) | [ep32-constant-current-source](./ep32-constant-current-source/) | 🟡 Summary only, simulation to do |
| 33 | Current mirrors and circuits without resistors: diode-connected transistor copies $I_{ref}$ to a matched twin ($I_{ref} = (V_{CC} - V_{BE})/R$, 15 V / 14.4 kΩ → 1 mA), assumptions (matching, high β), multiple outputs and $N_{out}/N_{in}$ ratios, mirrors replace resistors in ICs (REF200), emitter degeneration resistors (10 – 20 % of supply), Wilson mirror without resistors, spotting mirrors vs. sources (LM317 + 120 Ω), discrete 2N2222 test (25 mA → 120 mA unmatched, 26 mA with 75 Ω or Wilson) | [ep33-current-mirrors-circuits-without-resistors](./ep33-current-mirrors-circuits-without-resistors/) | 🟡 Summary only, simulation to do |
| 34 | The differential amplifier and why it is so popular | — | ⬜ |
| 35 | Advanced amplifier topics (bootstrap, Miller effect, cascode) | — | ⬜ |
| 36 | Oscillators (Hartley, Colpitts, crystal) with a practical experiment | — | ⬜ |
| 37 | Sending a wireless signal remotely (the simplest circuit) | — | ⬜ |

---

## Level 4

Episodes 38–51. Starts with the 555 timer.

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 38 | What's inside the 555 timer | — | ⬜ |
| 39 | A broader understanding of the 555 timer, with an interactive circuit design | — | ⬜ |
| 40 | Marketable 555 timer applications | — | ⬜ |
| 41 | Designing a sequential-function circuit for a factory with the 555 | — | ⬜ |
| 42 | Designing a driver-assist circuit for a car factory | — | ⬜ |
| 43 | How to make use of the CD4017 | — | ⬜ |
| 44 | Designing a multi-timer circuit that splits time between different tasks | — | ⬜ |
| 45 | The queen of sequential events, and designing a circuit for a food factory | — | ⬜ |
| 46 | Designing an efficient traffic light circuit without programming | — | ⬜ |
| 47 | Designing an interactive clap switch to turn a device on and off | — | ⬜ |
| 48 | Designing an interactive circuit to control light brightness with a single switch | — | ⬜ |
| 49 | Designing a simple electronic lock circuit without programming | — | ⬜ |
| 50 | Designing a circuit to control several devices remotely | — | ⬜ |
| 51 | Explaining various circuits that use the simplest components for many tasks | — | ⬜ |

---

## Level 5

Episodes 52–66.

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 52 | Introduction to Level 5: the purpose of these circuits and block-diagram design | — | ⬜ |
| 53 | Information sources used by professionals, and getting schematics | — | ⬜ |
| 54 | Information sources and how to choose ICs for specific functions | — | ⬜ |
| 55 | Information from other varied sources | — | ⬜ |
| 56 | Designing a lithium battery charger circuit with dedicated ICs | — | ⬜ |
| 57 | Developing a professional, competitive high-voltage probe | — | ⬜ |
| 58 | Developing a cheap, competitive light-intensity sensor | — | ⬜ |
| 59 | Analysing a market circuit: induction heating | — | ⬜ |
| 60 | Analysing a market circuit: high-frequency inverter | — | ⬜ |
| 61 | Analysing a market circuit: car inverter from the cigarette-lighter socket | — | ⬜ |
| 62 | Types of power converters | — | ⬜ |
| 63 | How the power inverter works | — | ⬜ |
| 64 | What is SPWM? | — | ⬜ |
| 65 | Developing a new frequency-detection circuit | — | ⬜ |
| 66 | Developing a new market circuit for temperature control with aluminium heat sinking | — | ⬜ |

---

## Level 6 – PCB Design

Episodes 67–77. Designing and manufacturing printed circuit boards (EasyEDA, isolation and creepage, SMD soldering, aluminium boards).

| Ep | Topic | Folder | Status |
|---|---|---|---|
| 67 | Introduction to the PCB course: what printed circuit boards are | — | ⬜ |
| 68 | Various practical examples of distinctive PCBs | — | ⬜ |
| 69 | PCB manufacturing processes by different methods, plus soldering | — | ⬜ |
| 70 | Designing the first PCB and an introduction to EasyEDA | — | ⬜ |
| 71 | Designing a PCB for a sound detector circuit | — | ⬜ |
| 72 | What matters about isolation in PCBs | — | ⬜ |
| 73 | What creepage means in PCB design and why it matters | — | ⬜ |
| 74 | SMD soldering and what a stencil is | — | ⬜ |
| 75 | Manufacturing a PCB on aluminium | — | ⬜ |
| 76 | Important considerations in PCB manufacturing for a more robust design | — | ⬜ |
| 77 | How to benefit from electronic design experience, practically and financially | — | ⬜ |

---

**Status key:** ✅ done · 🟡 in progress · ⬜ not started
