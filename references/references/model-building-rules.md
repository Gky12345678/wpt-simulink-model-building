# Model Building Rules

Use these rules after pre-build design alignment confirms the model purpose and topology.

## 1. Topology Description

Classify the WPT/BWPT system before arranging blocks:

- single-transmitter/single-receiver
- single-transmitter/multiple-receiver
- multiple-transmitter/single-receiver
- multiple-transmitter/multiple-receiver
- unidirectional WPT
- bidirectional BWPT
- diode-rectified receiver
- active-rectified receiver
- synchronous-rectified receiver
- DC/DC post-stage receiver
- direct AC load or equivalent load

Arrange by functional modules:

```text
DC/input port
-> high-frequency converter or excitation source
-> compensation network
-> transmitter coil or coil array
-> coupling relationship
-> receiver coil or coil array
-> receiver compensation network
-> rectifier/converter or AC load
-> DC-link/load/output port
```

Rules:

- Place transmitter or left-side modules on the left.
- Place receiver or right-side modules on the right.
- Place coupled coils or coupling modules between transmitter and receiver sides.
- Place compensation networks between converters and coils.
- Place DC ports, batteries, DC links, and loads on the outside.
- Place control and diagnostic blocks outside the main power path.
- Keep AC/high-frequency power path visually distinct from DC-link path.
- For BWPT, keep the left and right sides fixed even when power flow reverses.

Port orientation:

```text
DC ports face outward.
High-frequency AC ports face the coupling mechanism.
Control ports face downward or outward.
Measurement outputs leave toward the signal/diagnostic area.
```

Forbidden:

- Do not hide the complete main power circuit inside one large subsystem.
- Do not insert control modules into the main power path.
- Do not let scopes, displays, constants, or labels dominate the topology view.
- Do not omit branch numbering in multi-transmitter or multi-receiver systems.
- Do not hide multi-coil port correspondence without a clear matrix or port map.

## 2. Power-Device Modeling

Identify the model abstraction level:

```text
system-equivalent model
average model
ideal-switching model
device-level switching model
detailed device model
```

Do not present a system-equivalent model as a device-level model.

For device-level full bridges:

- Use four independent MOSFET/IGBT devices.
- Keep body diode direction visually clear.
- Connect every device gate input.
- Align upper and lower devices vertically.
- Align left and right bridge legs horizontally.
- Keep DC positive and negative rails visually clear.
- Take AC output from the two leg midpoints.
- Do not use Universal Bridge or packaged bridge blocks when explicit devices are required.

Recommended switch names:

```text
S1, S2, S3, S4
Q1, Q2, Q3, Q4
Tx_S1, Tx_S2, Tx_S3, Tx_S4
Rx_S1, Rx_S2, Rx_S3, Rx_S4
```

Prefer MOSFET visual orientation with drain up and source down. Do not rotate some switches only to shorten wires if that harms bridge readability.

For diode rectifiers:

- Use four explicit diodes or a clearly labeled diode-bridge module.
- Keep AC terminals facing receiver compensation.
- Keep DC terminals facing DC-link capacitor and load.
- Match diode orientation to expected rectification direction.
- Place DC-link capacitor close to rectifier output.

For active or synchronous rectifiers:

- Show controllable devices.
- Connect gate inputs to corresponding devices.
- Keep AC side facing receiver compensation.
- Keep DC side facing DC link/load.
- Route control signals from the control area, not through the power loop.

## 3. Compensation And Coupling

Use real inductor/capacitor components for compensation networks when topology clarity is required.

Rules:

- Series elements lie along the power path.
- Parallel elements branch vertically.
- Labels stay close to components.
- Component names match topology meaning: `Cp`, `Cs`, `Lfp`, `Cfp`, `Lf`, `Cf`.
- Components on the same side follow electrical order.
- Do not assume LCC/LCL/CLC branch order; confirm it or follow the provided circuit diagram.

Coupled-coil rules:

- Each coil clearly belongs to transmitter or receiver side.
- Single-pair coils should face each other.
- Multi-coil systems must number every coil.
- Mutual inductance or coupling coefficient must map to specific coils.
- Dot convention, reference direction, or current direction should be visible when relevant.
- If using a matrix model, preserve visible port-to-matrix correspondence.

## 4. Measurement And Observation

Measurements must support validation without cluttering the power circuit.

Common useful signals:

```text
transmitter bridge voltage
transmitter bridge current
receiver bridge or rectifier AC-side voltage
receiver bridge or rectifier AC-side current
DC-link voltage
load/output current
coil current when needed
compensation capacitor voltage when needed
```

Voltage sensors:

- Place across the exact two nodes being measured.
- Bridge-port voltage is measured between two bridge-leg midpoints.
- Compensation capacitor voltage is measured directly across capacitor terminals.
- DC-link voltage is measured across DC positive and negative rails.
- Keep sensors close to measured nodes.

Current sensors:

- Place in series with the measured branch.
- Keep direction consistent with signal naming.
- Place bridge current measurement at the AC output or resonant branch.
- Mark or name current direction when polarity matters.

Scope organization:

- Prefer one main Scope for key waveforms.
- Group transmitter, receiver, and DC-side signals.
- Use optional diagnostic scopes only when clearly separated.
- Recommended bridge-port validation: subplot 1 for Tx voltage/current, subplot 2 for Rx voltage/current.
- Use consistent colors: voltage black, current red, line width 2, white background, black axes/frame.

Signal naming examples:

```text
Tx_Bridge_V
Tx_Bridge_I
Rx_AC_V
Rx_AC_I
Tx_Coil_I
Rx_Coil_I
Vdc_Tx
Vdc_Rx
I_Load
```

## 5. Auxiliary Functional Modules

Separate the model into clear zones:

```text
Power Stage
Measurements
Signal Conditioning
Control
Modulation / PWM
Gate Driver
Protection / Fault Logic
Diagnostics / Scopes
Scenario / Sources
```

Encapsulate nontrivial auxiliary logic into subsystems:

```text
Control
PWM / Modulation
Gate_Driver
Protection
Signal_Conditioning
Diagnostics
Scenario / Test_Sequence
Logging
Reference_Generation
Mode_Logic
```

Main power circuit rule:

- Keep transmitter bridge, receiver bridge/rectifier, compensation networks, coupled coils, AC/DC boundary, and key measurement placement explicit when topology readability matters.

Auxiliary subsystem interface rules:

- Inputs and outputs must be named by physical meaning.
- Measurement inputs should enter from top or left.
- Control commands should exit toward PWM or gate driver.
- Gate outputs must map clearly to switch names.
- Enable, fault, and reset ports must be visible when used.
- Do not leave final subsystem ports named `In1`, `Out1`, `u`, or `y`.

Example:

```text
Gate_Driver_Tx
Inputs:  Tx_PWM1, Tx_PWM2, Tx_PWM3, Tx_PWM4, Gate_Enable
Outputs: Tx_G1,   Tx_G2,   Tx_G3,   Tx_G4
```

## 6. Parameter And Callback Management

Preferred rule:

- Put small and stable parameter sets directly in model `InitFcn`.
- Use a separate initialization `.m` script only when parameter volume or reuse justifies it.
- If using an external script, make its purpose clear and call it predictably.
- Do not keep unused initialization files.

Parameter names should reflect physical meaning:

```text
Lp, Ls
Cp, Cs
Lfp, Cfp
k, M
fs, f0, Ts
Vdc_tx, Vdc_rx
Rload
Cdc
Ron
deadtime
```

Derived parameters must be traceable:

```text
M = k * sqrt(Lp * Ls)
w0 = 2*pi*f0
Cp = 1/(w0^2*Lp)
Cs = 1/(w0^2*Ls)
Ts = 1/fs
```

Do not require manual workspace setup before opening or updating the model.

## 7. Layout And Routing

The model is a circuit schematic, not a PCB layout.

Optimize for:

```text
electrical correctness
topology readability
module separation
wire crossing reduction
label clarity
compactness
visual balance
```

Rules:

- Keep the main power path horizontal where possible.
- Keep power wiring orthogonal.
- Avoid diagonal lines.
- Keep similar modules aligned and similarly sized.
- Make transmitter and receiver sides visually balanced.
- Put coupled coils near the center.
- Put compensation components between converters and coils.
- Put measurement sensors near measured objects.
- Put control and diagnostic blocks below or beside the main power path.
- Use Goto/From or buses for long control/measurement signals, but not to hide important power connections.
- Measurement blocks should be smaller than main power devices.
- Labels must not cover wires or overlap neighboring modules.

## 8. Validation Readiness

Track only completed states:

```text
opened
updated
compiled
simulated
waveform_checked
layout_checked
```

Minimum completion checks:

```text
[ ] model opens without missing library blocks
[ ] parameters initialize without manual workspace setup
[ ] diagram updates without obvious errors
[ ] power topology is visually traceable
[ ] DC and AC sides are distinguishable
[ ] compensation networks are placed correctly
[ ] coupled coils or coupling matrix are traceable
[ ] converter/rectifier type is visually clear
[ ] gate/control inputs are connected where required
[ ] measurement sensors are placed near measured objects
[ ] key signals are routed to organized scopes or outputs
[ ] no unused test modules remain
[ ] no floating ports or invalid wires remain
[ ] layout has no severe crossing, overlap, or clutter
```
