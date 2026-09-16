# 19V to 12V and 5V Dual Buck Converter

## 1. Project Overview

This PCB takes a **19V DC input** and generates two regulated output voltages:

* **12V DC output**
* **5V DC output**

The board uses **buck converters (step-down switching regulators)** to reduce the 19V input voltage.

### Basic Power Flow

```text
19V DC INPUT
     |
     v
Input Protection
     |
     v
Input Filtering
     |
     +-------------------+
     |                   |
     v                   v
  12V BUCK            5V BUCK
     |                   |
     v                   v
   +12V                 +5V
```

The main purpose of this board is to provide stable lower voltages from a higher 19V DC supply.

---

# 2. Why Do We Need a Buck Converter?

Suppose we have a 19V adapter.

But our circuit needs only 5V.

We need to convert:

```text
19V ---> 5V
```

One simple idea is to use a linear regulator.

However, a linear regulator wastes the extra voltage as heat.

For example:

```text
Input  = 19V
Output = 5V

Voltage difference = 19V - 5V
                  = 14V
```

If the load current is 1A:

```text
Power lost = Voltage difference × Current

           = 14V × 1A
           = 14W
```

So 14W can be converted into heat.

A buck converter works differently.

It uses **high-speed switching, an inductor, a diode/MOSFET path, and capacitors** to transfer energy efficiently from the input to the output.

Therefore:

```text
19V
 |
 |  Buck Converter
 v
 5V
```

is much more suitable for many power-conversion applications.

---

# 3. What is a Buck Converter?

A buck converter is a **DC-to-DC step-down converter**.

Its job is:

```text
Higher DC Voltage
       |
       v
   Buck Converter
       |
       v
Lower DC Voltage
```

For this board:

```text
19V ---> 12V
19V ---> 5V
```

The important thing to understand is that the converter does not simply "remove" the extra voltage.

Instead, it rapidly switches the input ON and OFF.

Conceptually:

```text
ON   OFF   ON   OFF   ON   OFF
 |    |     |    |     |    |
 v    v     v    v     v    v
19V  0V    19V  0V    19V  0V
```

This switching happens very quickly.

The **inductor and capacitor** then help convert this switching waveform into a smooth DC output.

---

# 4. Main Parts of a Buck Converter

A basic buck converter contains:

```text
Input
  |
  v
Switch
  |
  v
Inductor
  |
  v
Output
  |
  v
Capacitor
```

There is also a current path for the inductor when the main switch is OFF.

Depending on the topology, this can be provided by:

* a diode, or
* a synchronous MOSFET.

The converter also has a control circuit that checks the output voltage.

---

# 5. Understanding the Main Idea: Switching

The switching element is turned ON and OFF very quickly.

## Switch ON

When the switch is ON:

```text
19V
 |
 v
Switch ON
 |
 v
Inductor
 |
 v
Load
```

Energy is transferred from the input to the inductor and load.

The inductor stores some of this energy in its magnetic field.

---

## Switch OFF

When the switch turns OFF, the inductor does not want its current to suddenly become zero.

The inductor tries to keep the current flowing.

Therefore, the current uses another available path through the diode or synchronous switch.

```text
Switch OFF
     |
     v
Inductor current continues
     |
     v
Diode / freewheel path
     |
     v
Load
```

This ON/OFF process repeats continuously.

---

# 6. What Does the Inductor Do?

The inductor is one of the most important components in a buck converter.

An inductor stores energy in a **magnetic field**.

The important property of an inductor is:

> An inductor does not allow its current to change instantly.

For example, if current is flowing through an inductor:

```text
1A ---> 1A ---> 1A ---> ...
```

The current cannot suddenly become:

```text
1A ---> 0A
```

without a response from the circuit.

This property is very useful in a switching converter.

The inductor helps make the output current smoother.

---

# 7. What Does the Capacitor Do?

A capacitor stores electrical energy.

Its main job in this converter is to help keep the output voltage smooth.

Without filtering, the switching stage produces a changing waveform.

Conceptually:

```text
Switching waveform:

  ┌───┐     ┌───┐     ┌───┐
  │   │     │   │     │   │
──┘   └─────┘   └─────┘   └──
```

The output capacitor helps reduce the voltage variation:

```text
Without enough filtering:
5V ~~~~ ripple ~~~~

With filtering:
5V ----------------
```

The output is not perfectly noise-free, but the capacitor reduces voltage ripple.

---

# 8. Understanding the Complete Buck Operation

The complete process can be understood as:

```text
19V INPUT
    |
    v
High-speed switching
    |
    v
Switching waveform
    |
    v
Inductor
    |
    v
Current becomes smoother
    |
    v
Output capacitor
    |
    v
Voltage becomes smoother
    |
    v
LOWER DC OUTPUT
```

At the same time:

```text
Output
   |
   v
Feedback circuit
   |
   v
Controller
   |
   v
Switching control
```

So the converter is continuously controlling itself.

---

# 9. Board-Level Power Flow

The complete board can be viewed like this:

```text
                 19V ADAPTER
                      |
                      v
                     J1
                      |
                      v
                  S1 / S2
                      |
                      v
                     F1
                      |
                      v
                 19V POWER RAIL
                      |
          +-----------+-----------+
          |                       |
          v                       v
      12V BUCK                 5V BUCK
          |                       |
          v                       v
        +12V                     +5V
```

The input protection and filtering components are placed before the main converter sections.

---

# 10. J1 — DC Input Connector

**J1 is the input connector.**

The external 19V DC adapter is connected here.

Typically:

```text
J1
 |
 +---- +19V
 |
 +---- GND
```

The 19V supply enters the PCB through J1.

From there, the power goes through the protection and switching sections.

---

# 11. S1 and S2 — Power Switches

S1 and S2 are used to control the power path.

When the switch is closed:

```text
19V ---> Switch ---> Circuit
```

Power can flow.

When the switch is open:

```text
19V ---> Switch X ---> Circuit
```

The power path is interrupted.

Therefore, the switches can be used to turn the circuit ON or OFF.

---

# 12. F1 — Fuse

F1 is the **fuse**.

Its main purpose is **over-current protection**.

Normally:

```text
19V ---> Fuse ---> Circuit
```

If a serious short circuit occurs:

```text
19V ---> Fuse ---> SHORT
```

The current can become very high.

The fuse is designed to open the circuit when its current limit is exceeded.

Then:

```text
19V ---> Fuse X ---> Circuit
```

The circuit is disconnected from the input supply.

### Important

A fuse mainly protects against **excessive current**.

It is not the same as a TVS diode.

---

# 13. D2 — TVS Diode

D2 is a **TVS (Transient Voltage Suppressor) diode**.

Its job is to help protect the circuit from short-duration voltage spikes.

For example, normally the input may be:

```text
19V ----------------------
```

But a transient may look like:

```text
19V ---------/\-----------
             ^
          voltage spike
```

The TVS diode can conduct strongly during a sufficiently high transient and help clamp the voltage.

Therefore:

```text
Fuse  ---> Over-current protection

TVS   ---> Voltage transient protection
```

These are two different types of protection.

---

# 14. Input Capacitors

The input section contains:

* C10 = 680uF electrolytic capacitor
* C1, C2, C3 = ceramic capacitors

These capacitors help filter the incoming 19V supply.

---

## 14.1 C10 — 680uF Electrolytic Capacitor

C10 is a relatively large capacitor.

It provides **bulk energy storage and filtering**.

Think of it like a small electrical energy tank.

If the input voltage has slower changes or the converter suddenly needs current, the capacitor can help support the supply locally.

---

## 14.2 C1, C2, C3 — Ceramic Capacitors

Ceramic capacitors are useful for handling high-frequency noise.

Switching converters operate at high frequencies, so fast current changes can create high-frequency noise.

Small ceramic capacitors placed close to the converter can help reduce this noise.

Therefore:

```text
Large electrolytic capacitor
        |
        v
Bulk filtering

Small ceramic capacitors
        |
        v
High-frequency filtering
```

Both types work together.

---

# 15. R1 and R2 — EN / UVLO Voltage Divider

R1 and R2 form a voltage divider connected to the regulator's EN/UVLO function.

Basic circuit:

```text
VIN
 |
R1
 |
 +-------> EN / UVLO
 |
R2
 |
GND
```

A voltage divider produces a lower voltage from a higher voltage.

The EN/UVLO pin uses this voltage to determine whether the input supply is high enough for normal operation.

---

# 16. What is UVLO?

UVLO means:

**Under-Voltage Lockout**

It means the regulator can remain OFF when the input voltage is too low.

For example:

```text
Input voltage high enough
        |
        v
Converter ON
```

But:

```text
Input voltage too low
        |
        v
Converter OFF
```

This helps prevent the regulator from operating incorrectly when the input supply is below the required level.

---

# 17. 19V to 12V Converter

The first converter section generates the 12V output.

Main IC:

**U1 = LM2596S-12**

Basic path:

```text
19V
 |
 v
LM2596S-12
 |
 v
L2 Inductor
 |
 v
C11
 |
 v
+12V
```

D4 provides the required current path during the switch OFF period in the buck converter.

---

# 18. U1 — LM2596S-12

U1 is the main switching regulator for the 12V section.

Its job is to convert the input supply into a regulated 12V output.

```text
INPUT
19V
 |
 v
U1
LM2596S-12
 |
 v
12V OUTPUT
```

The IC controls the switching process.

It does not work alone.

It works together with:

* L2
* D4
* C11
* input capacitors
* control circuitry

---

# 19. L2 — 12V Inductor

L2 is the inductor for the 12V buck converter.

It stores energy in its magnetic field during the switching cycle.

When the main switch is ON:

```text
Input
 |
 v
Switch
 |
 v
L2
 |
 v
Load
```

When the switch turns OFF, L2 tries to keep its current flowing.

The current then uses the available freewheel path.

Therefore L2 helps maintain continuous output current.

---

# 20. D4 — Schottky Diode

D4 is a Schottky diode used as the catch/freewheel diode in the asynchronous buck stage.

When the main switch is OFF:

```text
Main switch OFF
       |
       v
Inductor current continues
       |
       v
D4 provides current path
```

A Schottky diode is useful in switching power supplies because it has a relatively low forward voltage and fast switching behavior.

This helps reduce losses.

---

# 21. C11 — 220uF Output Capacitor

C11 is connected at the 12V output.

Its main functions are:

1. Reduce output voltage ripple.
2. Store energy.
3. Help support sudden load changes.

Conceptually:

```text
L2 output
   |
   v
C11
   |
   v
Cleaner +12V
```

---

# 22. Complete 12V Section

```text
              19V INPUT
                  |
                  v
          LM2596S-12 (U1)
                  |
           Switching action
                  |
                  v
              L2 Inductor
                  |
          +-------+-------+
          |               |
          |              D4
          |               |
          v               |
        C11 <-------------+
          |
          v
        +12V
```

The exact current loop depends on the actual schematic topology, but the basic purpose of these components is as described above.

---

# 23. 19V to 5V Converter

The second converter generates the 5V output.

Main IC:

**U2 = TPS54531DDAR**

Basic path:

```text
19V
 |
 v
TPS54531
 |
 v
Switching stage
 |
 v
L1
 |
 v
Output capacitors
 |
 v
+5V
```

The important difference is that this converter uses a feedback system to regulate the output.

---

# 24. U2 — TPS54531DDAR

U2 is the main switching regulator for the 5V section.

Its job is to control the switching process so that the output stays around the required 5V level.

```text
19V INPUT
    |
    v
TPS54531
    |
    v
Switching
    |
    v
Inductor
    |
    v
5V OUTPUT
```

The IC continuously uses feedback to control the switching.

---

# 25. What is Feedback?

Feedback means:

> The circuit measures its own output and sends information back to the controller.

For the 5V converter:

```text
5V OUTPUT
    |
    v
Feedback resistors
    |
    v
VSENSE
    |
    v
TPS54531
```

The controller uses this information to decide how to control the switching.

---

# 26. R4 and R5 — Feedback Resistors

R4 and R5 form a voltage divider.

Conceptually:

```text
             +5V
              |
             Rtop
              |
              +-------> VSENSE
              |
           Rbottom
              |
             GND
```

The feedback pin does not need the full 5V.

The resistor divider creates a smaller voltage that the IC can measure.

For the usual arrangement:

```text
Rtop    = VOUT to VSENSE
Rbottom = VSENSE to GND
```

The output voltage is approximately:

```text
VOUT = VREF × (1 + Rtop/Rbottom)
```

For TPS54531:

```text
VREF ≈ 0.8V
```

The resistor values are selected so that the feedback voltage reaches the required reference when the output is near 5V.

---

# 27. How Feedback Maintains 5V

Suppose the output is correct:

```text
5V
 |
 v
Feedback
 |
 v
VSENSE = correct value
 |
 v
Controller
```

The controller continues normal switching.

Now suppose the load increases.

For example:

```text
Sensor turns ON
        |
        v
Current demand increases
        |
        v
Output may drop slightly
```

Suppose:

```text
5.0V ---> 4.8V
```

The feedback voltage also decreases.

The controller detects this difference and changes the switching operation to transfer more energy to the output.

Conceptually:

```text
Output drops
     |
     v
Feedback detects it
     |
     v
Controller reacts
     |
     v
Switching changes
     |
     v
More energy transferred
     |
     v
Output moves back toward 5V
```

---

# 28. What if Output Becomes Too High?

Suppose the output rises:

```text
5.0V ---> 5.2V
```

The feedback voltage also increases.

The controller detects that the output is too high and adjusts the switching operation to reduce the energy transferred to the output.

Conceptually:

```text
Output too high
      |
      v
Feedback increases
      |
      v
Controller detects it
      |
      v
Switching is adjusted
      |
      v
Output moves back toward target
```

This is called **closed-loop regulation**.

---

# 29. Closed-Loop Control

The complete feedback loop is:

```text
       +---------------------------+
       |                           |
       |                           v
19V -> Converter -> 5V OUTPUT -> Feedback
          ^                         |
          |                         v
          +------ Controller <------+
```

The controller continuously checks the output and corrects the switching.

This is one of the most important concepts in a switching regulator.

---

# 30. L1 — 4.7uH Inductor

L1 is the main inductor in the 5V converter.

Its job is similar to L2 in the 12V converter.

It:

* stores energy in a magnetic field
* helps keep current flowing
* smooths the switching current

Basic path:

```text
TPS54531
    |
    v
Switching node
    |
    v
L1 = 4.7uH
    |
    v
5V OUTPUT
```

---

# 31. D1 — Schottky Diode

D1 provides the required freewheel/catch current path in the external-diode buck topology.

When the main switch is OFF:

```text
Main switch OFF
       |
       v
Inductor current continues
       |
       v
D1 current path
```

The exact diode connection and operating behavior should always be checked against the actual TPS54531 schematic and datasheet.

---

# 32. C7 — Bootstrap Capacitor

C7 is a bootstrap capacitor.

This is a more advanced part of the circuit.

A high-side switching MOSFET may need a gate-drive voltage that is higher than the switching node voltage.

The bootstrap circuit provides the required stored charge for this gate-drive function.

Simple idea:

```text
Bootstrap capacitor
        |
        v
Stores charge
        |
        v
Provides gate-drive support
        |
        v
High-side switch can operate correctly
```

Important:

**C7 is not the main output filtering capacitor.**

Its purpose is related to the switching/gate-drive circuit.

---

# 33. C6 — Soft-Start Capacitor

C6 is used for soft-start.

Without controlled startup, the output would try to rise very quickly.

Soft-start makes the output voltage rise in a controlled way.

Conceptually:

```text
Without soft-start:

0V ------------------> 5V
       sudden


With soft-start:

0V
 |
 |       /
 |      /
 |     /
 |____/--------------> 5V
```

Soft-start helps control startup behavior and reduces stress during power-up.

---

# 34. Compensation Network

The feedback system must be stable.

The controller is continuously doing this:

```text
Measure output
      |
      v
Compare with target
      |
      v
Change switching
      |
      v
Output changes
      |
      +------> Measure again
```

If the feedback loop is badly designed, the output can show:

* oscillation
* overshoot
* undershoot
* poor transient response

The compensation components help control the behavior of this feedback loop.

In your circuit, components such as:

```text
R3
C4
C5
```

are part of the control/compensation network, depending on their exact connections in the schematic.

Simple definition:

> **Compensation is used to make the feedback control loop stable and well behaved.**

---

# 35. C8 and C9 — 5V Output Capacitors

C8 and C9 are output capacitors for the 5V rail.

Their main jobs are:

### 1. Reduce output ripple

Switching creates small voltage variations.

The capacitors help reduce these variations.

### 2. Support load changes

If a load suddenly needs more current, the capacitor can provide some current for a short time while the regulator responds.

Conceptually:

```text
Load suddenly increases
        |
        v
Output needs more current
        |
        v
Capacitor provides temporary support
        |
        v
Regulator increases energy transfer
```

The output capacitor therefore helps both steady-state filtering and transient response.

---

# 36. What is Output Ripple?

A switching regulator does not produce a perfectly flat voltage.

For example:

```text
Ideal 5V:

5.000V ------------------------

Real switching output:

5V ----~~~~----~~~~----~~~~----
```

The small variation is called **ripple**.

Capacitors, inductor selection, switching frequency, PCB layout, ESR, and control-loop design all affect ripple.

Therefore, it is better to say:

> The output capacitors reduce ripple.

Not:

> The output is completely noise-free.

---

# 37. 5V Section — Complete Picture

```text
                       19V
                        |
                        v
                 +-------------+
                 | TPS54531 U2 |
                 +------+------+
                        |
                        v
                 Switching Node
                        |
                        v
                    L1 4.7uH
                        |
                        +---------> +5V
                        |
                      C8/C9
                        |
                       GND


+5V
 |
 v
Rtop
 |
 +-------> VSENSE
 |
Rbottom
 |
GND
 |
 +---------------------> TPS54531
```

Additional control components:

```text
C7 -> Bootstrap function
C6 -> Soft-start
R3/C4/C5 -> Compensation/control-loop function
D1 -> Catch/freewheel path, depending on the topology
```

---

# 38. 12V and 5V Sections Compared

| Feature          | 12V Section                                      | 5V Section             |
| ---------------- | ------------------------------------------------ | ---------------------- |
| Input            | 19V                                              | 19V                    |
| Main IC          | LM2596S-12                                       | TPS54531DDAR           |
| Output           | 12V                                              | 5V                     |
| Main Inductor    | L2                                               | L1                     |
| Diode            | D4                                               | D1                     |
| Output Capacitor | C11                                              | C8, C9                 |
| Feedback         | Depends on fixed-output regulator implementation | R4/R5 feedback divider |
| Soft-start       | IC-dependent                                     | C6                     |
| Bootstrap        | IC-dependent                                     | C7                     |
| Compensation     | IC-dependent                                     | R3/C4/C5 network       |

---

# 39. Important Difference Between the Two Regulators

The basic idea of both sections is the same:

```text
19V
 |
 v
Switching
 |
 v
Inductor
 |
 v
Capacitor
 |
 v
Lower DC voltage
```

But the regulator ICs are different.

The component values and control circuits are selected according to the specific regulator.

Therefore, **do not assume that a component from the 12V section can simply be copied into the 5V section.**

---

# 40. What Happens From the Moment Power Is Connected?

Let's follow the complete sequence.

## Step 1 — Adapter connected

```text
19V Adapter
     |
     v
    J1
```

19V enters the PCB.

---

## Step 2 — Power passes through switches

```text
J1
 |
 v
S1/S2
```

If the switches are ON, the power path continues.

---

## Step 3 — Fuse provides protection

```text
S1/S2
  |
  v
 F1
```

The fuse protects against excessive current.

---

## Step 4 — TVS and input capacitors protect/filter the supply

```text
       19V
        |
   +----+----+
   |         |
  TVS      Capacitors
   |         |
   +----+----+
        |
        v
   Cleaned input
```

---

## Step 5 — Buck converters start

The 19V rail reaches both converter sections.

```text
             19V
              |
       +------+------+
       |             |
       v             v
    12V Buck       5V Buck
```

---

## Step 6 — Switching starts

The regulator IC controls the switching element.

```text
ON -> OFF -> ON -> OFF -> ...
```

---

## Step 7 — Inductor transfers and stores energy

```text
Switching
    |
    v
Inductor
    |
    v
Energy transfer
```

---

## Step 8 — Capacitors smooth the output

```text
Inductor
   |
   v
Output capacitor
   |
   v
Stable DC output
```

---

## Step 9 — Feedback checks the 5V output

```text
5V
 |
 v
R4/R5
 |
 v
VSENSE
 |
 v
TPS54531
```

The controller continuously adjusts the switching.

---

# 41. Why Are There So Many Capacitors?

A common beginner question is:

> "Why not use only one capacitor?"

Because capacitors have different values and electrical characteristics.

For example:

```text
680uF electrolytic
       |
       v
Bulk energy storage
```

while:

```text
Small ceramic capacitors
       |
       v
High-frequency filtering
```

And:

```text
Output capacitors
       |
       v
Output ripple + transient support
```

The location of a capacitor is also important.

A capacitor electrically close to the switching IC can be much more effective for high-frequency current than the same capacitor placed far away.

---

# 42. Why PCB Layout Is Important

A buck converter switches current very quickly.

Fast current changes create unwanted effects such as:

* parasitic inductance
* voltage spikes
* ringing
* EMI
* additional noise

Therefore PCB layout is part of the circuit design.

For example:

```text
Bad:

IC -------- long trace -------- capacitor
```

can be worse than:

```text
Good:

IC -- short trace -- capacitor
```

The exact layout must follow the regulator datasheet/layout recommendations.

---

# 43. Important PCB Layout Areas

For a buck converter, pay special attention to:

### Input capacitor

Keep the high-current input loop compact.

```text
VIN capacitor
     |
     v
Switching IC
```

### Inductor

Keep it close to the switching stage.

### Output capacitor

Keep it close to the inductor/output path.

### Feedback trace

Keep the feedback sensing path away from noisy switching nodes where practical.

### Ground

Use a good ground return path and follow the regulator's recommended layout.

---

# 44. Power Path vs Feedback Path

This is very important for PCB design.

## Power path

Carries significant current.

```text
19V
 |
 v
Switch
 |
 v
Inductor
 |
 v
Output
```

These traces may need to be wider depending on current.

---

## Feedback path

Measures voltage.

```text
Output
 |
 v
Feedback resistors
 |
 v
VSENSE
 |
 v
IC
```

This is a sensing signal, not the main power path.

It should be routed carefully to avoid picking up switching noise.

---

# 45. Voltage and Current Are Different

Always remember:

```text
Voltage = electrical potential difference

Current = flow of electric charge
```

Example:

```text
5V @ 2A
```

means:

* Voltage = 5V
* Maximum/load current = 2A in that example
* Power = 5 × 2 = 10W

Voltage does not mean the circuit automatically pushes that exact current.

The load determines how much current it draws, within the source/regulator limits.

---

# 46. Power Relationship

Electrical power is:

```text
P = V × I
```

For example:

```text
5V × 3A = 15W
```

If a buck converter is 90% efficient:

```text
Output power = 15W

Input power ≈ 15W / 0.90
            ≈ 16.67W
```

At 19V input:

```text
Input current ≈ 16.67W / 19V
              ≈ 0.88A
```

This shows why a switching converter can convert a higher voltage at a lower current into a lower voltage at a higher current, while accounting for efficiency and losses.

---

# 47. Duty Cycle

Duty cycle tells us how much of each switching period the switch is ON.

For example:

```text
ON  = 30%
OFF = 70%
```

Duty cycle:

```text
D = 30%
```

For an ideal buck converter:

```text
VOUT ≈ D × VIN
```

For 19V to 5V:

```text
D ≈ 5 / 19
D ≈ 0.263
D ≈ 26.3%
```

For 19V to 12V:

```text
D ≈ 12 / 19
D ≈ 0.632
D ≈ 63.2%
```

These are ideal approximations.

Real circuits have losses and control behavior, so the actual duty cycle is not exactly these values.

---

# 48. The Most Important Concepts to Remember

If you forget everything else, remember these:

### 1. Buck Converter

A buck converter reduces a DC voltage.

```text
19V ---> Buck ---> 5V
```

### 2. Switching

The converter rapidly turns the switching element ON and OFF.

### 3. Inductor

The inductor stores energy in a magnetic field and helps maintain continuous current.

### 4. Diode / Freewheel Path

When the main switch is OFF, the inductor needs a path for its current.

### 5. Capacitor

The capacitor stores energy and helps keep the output voltage smooth.

### 6. Feedback

Feedback tells the controller what the output voltage is doing.

### 7. Fuse

Fuse protects mainly against excessive current.

### 8. TVS

TVS helps protect against fast voltage transients.

### 9. UVLO

UVLO prevents operation when the input voltage is below the configured threshold.

### 10. Soft-Start

Soft-start controls how quickly the output voltage rises during startup.

### 11. Compensation

Compensation helps keep the feedback control loop stable.

### 12. PCB Layout

Good PCB layout is very important because switching currents change very quickly.

---

# 49. Complete Circuit — One Final View

```text
                         19V DC ADAPTER
                                |
                                v
                               J1
                                |
                                v
                            S1 / S2
                                |
                                v
                               F1
                                |
                                v
                         +19V POWER RAIL
                                |
                    +-----------+-----------+
                    |                       |
                    v                       v
              INPUT FILTER             INPUT FILTER
                    |                       |
                    v                       v
             LM2596S-12 U1             TPS54531 U2
                    |                       |
                    v                       v
                  L2                    Switching
                    |                       |
                 D4 |                       v
                    |                      L1
                    v                       |
                  C11                    C8/C9
                    |                       |
                    v                       v
                  +12V                     +5V
                                            |
                                            v
                                        R4 / R5
                                            |
                                            v
                                         VSENSE
                                            |
                                            v
                                      TPS54531 U2
                                            |
                                            v
                                      Switching
                                      control
```

---

# 50. Final Mental Model

Think about the complete board like this:

```text
                 INPUT
                  19V
                   |
                   v
          +----------------+
          |   PROTECTION   |
          | Fuse + TVS     |
          +-------+--------+
                  |
                  v
          +----------------+
          |    FILTERING   |
          |   Capacitors   |
          +-------+--------+
                  |
          +-------+-------+
          |               |
          v               v
       12V BUCK         5V BUCK
          |               |
          v               v
      Inductor         Inductor
          |               |
          v               v
      Capacitor        Capacitor
          |               |
          v               v
        +12V             +5V
                            |
                            v
                         Feedback
                            |
                            v
                        Controller
                            |
                            v
                       Switching
                            |
                            +----> Output is corrected
```

## One sentence to remember

**This PCB takes 19V DC, protects and filters it, then uses two switching buck converters to efficiently generate regulated 12V and 5V outputs; the 5V regulator uses feedback to continuously monitor and correct its output voltage.**

---

# Quick Revision

```text
J1
↓
19V Input

S1/S2
↓
Power ON/OFF

F1
↓
Over-current protection

D2 TVS
↓
Transient voltage protection

C10
↓
Bulk filtering

C1/C2/C3
↓
High-frequency filtering

R1/R2
↓
EN/UVLO threshold

U1 + L2 + D4 + C11
↓
19V → 12V

U2 + L1 + D1 + C8/C9
↓
19V → 5V

R4/R5
↓
5V feedback

C6
↓
Soft-start

C7
↓
Bootstrap function

R3/C4/C5
↓
Control-loop compensation
```

**Core concept:**

```text
19V
 ↓
Protection
 ↓
Filtering
 ↓
Switching
 ↓
Inductor
 ↓
Capacitor
 ↓
12V / 5V
 ↓
Feedback
 ↓
Controller
 ↓
Correct switching
```
