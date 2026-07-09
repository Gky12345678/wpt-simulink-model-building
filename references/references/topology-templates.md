# Topology Templates

Use templates only after design alignment confirms the system type. A template provides model-building structure, not control-theory content.

## Template Selection

Use Template A for:

```text
single transmitter
single receiver
series compensation on both sides
unidirectional power flow
receiver is diode rectifier, active rectifier, synchronous rectifier, or load
```

Use Template B for:

```text
single left-side port
single right-side port
both sides have active bridges
both sides use LCC compensation
bidirectional power flow is required
```

Use Template C for:

```text
two transmitter branches
two receiver branches
each branch uses series compensation
multi-coupling mapping must be visible
```

Do not force a template if none matches.

## Template A: SS Unidirectional WPT

Topology chain:

```text
DC Source
-> Tx Inverter
-> Tx Series Capacitor Cp
-> Tx Coil Lp
-> Coupling M/k
-> Rx Coil Ls
-> Rx Series Capacitor Cs
-> Rx Rectifier or AC Load
-> DC Link / Load
```

Recommended top-level layout:

```text
[DC Source] [Tx Bridge] [Cp] [Lp]  ||coupling||  [Ls] [Cs] [Rx Rectifier] [DC Link/Load]
```

Confirm before building:

```text
[ ] Tx converter: full bridge / half bridge / equivalent AC source
[ ] Rx conversion: diode bridge / active bridge / synchronous bridge / AC load
[ ] Device level: packaged blocks / ideal switches / explicit MOSFETs or diodes
[ ] Library family: Simscape Electrical / Specialized Power Systems / reference-model style
[ ] Coupling model: mutual inductance / coupling coefficient / transformer block
[ ] Parameters: user-given / calculated from L and f0
[ ] DC-link capacitor enabled on receiver side: yes/no
[ ] Required measurements and Scope grouping
```

Rules:

- Put transmitter on the left and receiver on the right.
- Put `Cp` between Tx bridge and Tx coil.
- Put `Cs` between Rx coil and receiver rectifier/load.
- Keep `Lp` and `Ls` visually facing each other.
- Place coupling label or mutual-inductance block between coils.
- Do not build before receiver conversion type is confirmed.

Minimum measurements:

```text
Tx_Bridge_V
Tx_Bridge_I
Rx_AC_V
Rx_AC_I
Vdc_Rx
I_Load
```

Optional:

```text
V_Cp
V_Cs
I_Lp
I_Ls
```

Preferred Scope:

```text
subplot 1: Tx_Bridge_V, Tx_Bridge_I
subplot 2: Rx_AC_V, Rx_AC_I
subplot 3 optional: Vdc_Rx, I_Load
```

Forbidden:

- Do not add receiver gate-driver subsystem for a diode-only rectifier.
- Do not hide `Cp/Lp/Ls/Cs` inside an unlabeled impedance block.
- Do not put the load before the rectifier.
- Do not measure Rx AC voltage from one terminal to ground unless that is the intended quantity.
- Do not mix diode and active rectifier symbols.

## Template B: LCC-LCC Bidirectional BWPT

Topology chain:

```text
Left DC Port
-> Left Active Bridge
-> Left LCC Compensation
-> Left Coil
-> Coupling M/k
-> Right Coil
-> Right LCC Compensation
-> Right Active Bridge
-> Right DC Port
```

Recommended top-level layout:

```text
[Left DC Port] [Left Bridge] [Left LCC] [Left Coil] ||coupling|| [Right Coil] [Right LCC] [Right Bridge] [Right DC Port]
```

Confirm before building:

```text
[ ] Left/right side names: Left/Right, Tx/Rx, Primary/Secondary, 原边/副边
[ ] Both sides active bridges: yes/no
[ ] Bridge device level: packaged / ideal switches / explicit MOSFETs
[ ] Exact LCC structure on each side
[ ] LCC branches visible at top level or inside named compensation subsystems
[ ] Coupling model
[ ] DC ports: voltage source / battery / DC bus / load / bidirectional port
[ ] Gate driver detail level
[ ] Protection enabled: yes/no
[ ] Required bridge, coil, and LCC branch measurements
```

Hard rule:

```text
Do not assume an LCC branch order. Confirm or follow the provided circuit diagram.
```

Rules:

- Keep the left and right sides structurally symmetric.
- Do not rename sides based on temporary power-flow direction.
- Place each LCC network between its bridge and coil.
- Keep LCC series path horizontal.
- Keep LCC shunt branch vertical.
- Use the same visual style for both bridges.
- Use matching compensation-network drawing style on both sides.

Minimum measurements:

```text
Left_Bridge_V
Left_Bridge_I
Right_Bridge_V
Right_Bridge_I
Vdc_Left
Vdc_Right
```

Recommended:

```text
Left_Coil_I
Right_Coil_I
Left_LCC_Series_I
Right_LCC_Series_I
Left_LCC_Cap_V
Right_LCC_Cap_V
```

Preferred Scope:

```text
subplot 1: Left_Bridge_V, Left_Bridge_I
subplot 2: Right_Bridge_V, Right_Bridge_I
subplot 3: Vdc_Left, Vdc_Right
subplot 4 optional: key LCC branch currents or capacitor voltages
```

Forbidden:

- Do not call a model BWPT if one side is only a diode rectifier unless explicitly intended as asymmetric topology.
- Do not hide bridge gate-driver mapping.
- Do not route control wires through LCC components.
- Do not use different LCC drawing conventions on left and right without reason.
- Do not bury the exact LCC topology in a generic `Compensation` block when topology clarity is required.

## Template C: 2-to-2 Full-Series-Compensated WPT

Use for a multi-transmitter/multi-receiver WPT system with two transmitter branches and two receiver branches, where each coil branch uses series compensation.

Default two-row layout:

```text
Row 1:
Tx1_Port -> Tx1_Converter -> Cp1 -> Lp1  || coupling area ||  Ls1 -> Cs1 -> Rx1_Conversion -> Rx1_Output

Row 2:
Tx2_Port -> Tx2_Converter -> Cp2 -> Lp2  || coupling area ||  Ls2 -> Cs2 -> Rx2_Conversion -> Rx2_Output
```

Confirm before building:

```text
[ ] Tx1 and Tx2 are independent sources/converters or share one source/converter
[ ] Rx1 and Rx2 are independent receivers or share a DC bus
[ ] Rx conversion type for each branch
[ ] All four Tx-Rx couplings enabled: k11, k12, k21, k22
[ ] Tx-Tx coupling enabled: yes/no
[ ] Rx-Rx coupling enabled: yes/no
[ ] Branch parameters identical or branch-specific
[ ] Coupling represented by drawn links, matrix block, or multi-winding coupling block
[ ] Layout should be two-row, matrix-style, or another specified arrangement
```

Hard rule:

```text
Do not assume shared DC bus, identical parameters, or intra-side coupling without confirmation.
```

Coupling mapping:

```text
M11 / k11: Tx1_Coil -> Rx1_Coil
M12 / k12: Tx1_Coil -> Rx2_Coil
M21 / k21: Tx2_Coil -> Rx1_Coil
M22 / k22: Tx2_Coil -> Rx2_Coil
```

If explicit coupling lines become cluttered, use a `Coupling_Matrix` subsystem or block with visible ports and parameters:

```text
Ports: Tx1_Coil, Tx2_Coil, Rx1_Coil, Rx2_Coil
Parameters: M11, M12, M21, M22, optional M_Tx12, M_Rx12
```

Branch names:

```text
Tx1_Port, Tx1_Bridge, Tx1_Cp, Tx1_Coil
Tx2_Port, Tx2_Bridge, Tx2_Cp, Tx2_Coil
Rx1_Coil, Rx1_Cs, Rx1_Rectifier, Rx1_Output
Rx2_Coil, Rx2_Cs, Rx2_Rectifier, Rx2_Output
```

Minimum measurements:

```text
Tx1_Bridge_V
Tx1_Bridge_I
Tx2_Bridge_V
Tx2_Bridge_I
Rx1_AC_V
Rx1_AC_I
Rx2_AC_V
Rx2_AC_I
```

Preferred Scope:

```text
subplot 1: Tx1_Bridge_V, Tx1_Bridge_I
subplot 2: Tx2_Bridge_V, Tx2_Bridge_I
subplot 3: Rx1_AC_V, Rx1_AC_I
subplot 4: Rx2_AC_V, Rx2_AC_I
subplot 5 optional: output/DC-bus quantities
```

Forbidden:

- Do not omit branch numbers.
- Do not assume all couplings exist.
- Do not assume intra-side coupling exists.
- Do not assume Rx1/Rx2 share one output bus.
- Do not mix branch parameter names.
- Do not route Tx1 gate/control through the Tx2 bridge area.
- Do not draw a coupling matrix without visible port-to-index mapping.
- Do not hide all branches inside one subsystem.
