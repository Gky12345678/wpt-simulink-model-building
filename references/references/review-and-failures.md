# Review And Failure Corrections

Use this file before calling a WPT/BWPT Simulink model complete, or when cleaning up an existing model.

## 1. Review Checklist

### Design Alignment

```text
[ ] Model purpose is confirmed
[ ] Topology class is confirmed
[ ] Power direction is confirmed
[ ] Compensation topology is confirmed
[ ] Receiver conversion type is confirmed
[ ] Device abstraction level is confirmed
[ ] Library family is confirmed
[ ] Enabled auxiliary modules are confirmed
[ ] Required measurements are confirmed
[ ] Naming and layout preferences are confirmed
```

Report assumptions when any item was assumed instead of confirmed.

### Topology

```text
[ ] Transmitter/left-side modules are visually identifiable
[ ] Receiver/right-side modules are visually identifiable
[ ] DC ports face outward
[ ] AC/high-frequency ports face coupling region
[ ] Compensation networks sit between converter and coil
[ ] Coupled coils or coupling matrix are in the coupling region
[ ] Receiver rectifier/converter type is visually distinguishable
[ ] DC-link and load are placed after rectifier/converter
[ ] Multi-branch systems have visible branch numbering
[ ] Multi-coupling systems have traceable coupling relationships
[ ] Main power path is explicit and readable
```

### Power Devices

```text
[ ] Full bridges contain the required number of devices
[ ] MOSFET/IGBT orientation is consistent
[ ] Diode direction is clear
[ ] DC rails are visually distinct
[ ] AC bridge terminals come from leg midpoints
[ ] Gate terminals are visible and connected when required
[ ] Packaged bridge blocks are only used when allowed by model level
[ ] Diode rectifier is not drawn as active bridge
[ ] Active/synchronous bridge is not drawn as diode bridge
[ ] Device names and gate names match
```

### Compensation And Coupling

```text
[ ] Series compensation components are on the main power path
[ ] Parallel compensation branches are vertical and local
[ ] LCC/LCL/CLC branch order is confirmed or follows reference diagram
[ ] Compensation labels are close to components
[ ] Coil labels and branch labels are clear
[ ] Mutual inductance/coupling coefficients map to coil pairs
[ ] Coupling matrix indices match visible port numbering
```

### Measurement

```text
[ ] Voltage sensors are across the intended two nodes
[ ] Current sensors are in series with intended branches
[ ] Measurement polarity is clear or named
[ ] Sensors are close to measured components
[ ] PS-Simulink or measurement conversion blocks are near sensors
[ ] Required bridge-port voltage/current signals are exposed
[ ] DC-link/load signals are exposed when needed
[ ] No unused measurement ports remain visible
```

### Auxiliary Subsystems

```text
[ ] Control module is subsystemed if nontrivial
[ ] PWM/modulation module is subsystemed if nontrivial
[ ] Gate-driver module is subsystemed and close to bridge
[ ] Signal-conditioning module is between measurements and control
[ ] Protection module has visible enable/fault path
[ ] Scenario/test sequence module is separated from controller
[ ] Diagnostics/logging are grouped
[ ] Subsystem ports are meaningful, not In1/Out1
[ ] No unrelated logic is mixed into one large subsystem
```

### Parameter And Callback

```text
[ ] Parameters initialize without manual workspace setup
[ ] InitFcn or parameter script is used consistently
[ ] Unused initialization files are removed or ignored intentionally
[ ] Base parameters appear before derived parameters
[ ] Units are clear
[ ] No duplicate/conflicting parameter definitions exist
[ ] Model-building scripts are not confused with parameter-initialization scripts
```

### Layout And Visual

```text
[ ] Power path is mostly horizontal
[ ] Wires are mostly orthogonal
[ ] Severe diagonal lines are removed
[ ] Wire crossings are minimized
[ ] Labels do not cover wires
[ ] Blocks do not overlap
[ ] Measurement blocks are not oversized
[ ] Gate/control wires do not pass through the resonant tank
[ ] Scopes do not dominate the schematic
[ ] Transmitter and receiver sides are visually balanced
[ ] Top-level model has no abandoned debug blocks
```

### Scope And Logging

```text
[ ] Main Scope groups related waveforms
[ ] Transmitter and receiver signals are separated by subplot or grouping
[ ] Voltage and current colors are consistent
[ ] Signal legends/names are meaningful
[ ] To Workspace blocks use unique names
[ ] Temporary debug scopes are removed from final model
```

## 2. Failure Modes And Corrections

### Main Power Path Hidden

Failure: The full WPT/BWPT power path is hidden inside one large subsystem.

Correction: Expose converter/rectifier, compensation network, coupled coils, AC/DC boundaries, and key measurement placement. Subsystem control, driver, protection, diagnostics, and repeated branch internals only when top-level ports remain clear.

### Wrong Template Assumption

Failure: A diode-rectified receiver is modeled as an active bridge, or a single-receiver model is arranged as multi-receiver.

Correction: Return to design alignment. Confirm receiver conversion type, branch count, and power direction before editing topology.

### Compensation Network Scattered

Failure: `Cp`, `Cs`, LCC components, or shunt branches are far from their converter/coil or arranged out of electrical order.

Correction: Move compensation components between converter and coil. Keep series elements on the main path and shunt elements vertical and local. Confirm branch order for LCC/LCL/CLC networks.

### Coupling Relationship Untraceable

Failure: Multiple coils or coupling labels are tangled, floating, or not mapped to branch numbers.

Correction: Number every coil branch. Use `Mij/kij` names or a coupling matrix with visible port-to-index mapping. Avoid drawing all coupling lines if a matrix block is clearer.

### Bridge Port Ambiguous

Failure: AC bridge output is not clearly taken from the two leg midpoints.

Correction: Align bridge legs, expose midpoints, and route AC terminals from midpoint nodes. Place bridge voltage measurement directly across the two midpoints.

### MOSFET Orientation Inconsistent

Failure: Some MOSFETs are flipped or rotated inconsistently, making bridge legs hard to read.

Correction: Use consistent orientation, preferably drain up and source down. Keep upper/lower devices vertically aligned and bridge legs horizontally aligned.

### Gate Inputs Floating

Failure: Switch-level devices have unconnected gate terminals.

Correction: Create or connect a `Gate_Driver` subsystem. Map each gate output to one switch, for example `Tx_G1 -> Tx_S1`.

### Gate Signals Clutter Power Circuit

Failure: Gate wires cross DC rails, resonant tank, or coupling region.

Correction: Place the gate-driver subsystem near its bridge. Route short gate wires locally. Use buses or Goto/From tags for long logic signals while keeping final gate mapping visible.

### Control Logic Scattered

Failure: Gain, Sum, Step, logic, PWM, and protection blocks are scattered around the power stage.

Correction: Group nontrivial logic into functional subsystems: `Signal_Conditioning`, `Control`, `PWM`, `Gate_Driver`, `Protection`, `Scenario`, `Diagnostics`.

### Subsystem Used To Hide Mess

Failure: A subsystem has meaningless ports such as `In1/Out1` and hides crossed wires or unrelated logic.

Correction: Rename ports by physical meaning. Split unrelated functions into separate subsystems. Keep subsystem boundaries functional.

### Measurement Sensor Oversized

Failure: Voltage/current measurement blocks are larger than power devices or dominate the schematic.

Correction: Resize sensors smaller than main devices. Place them close to measured nodes. Route measurement outputs away from the power path.

### Measurement Polarity Unclear

Failure: Current or voltage sign convention cannot be inferred from sensor placement or name.

Correction: Place sensors with consistent direction. Rename signals to match polarity or add clear local labels.

### Scope Clutter

Failure: Many small scopes are scattered across the model.

Correction: Use one main Scope for key waveforms and an optional diagnostic Scope for secondary signals. Group transmitter, receiver, and DC-side signals.

### Signal Names Too Generic

Failure: Signals are named `u`, `y`, `data`, `out`, `In1`, `Out1`, or similar.

Correction: Rename by object and physical quantity, such as `Tx_Bridge_V`, `Rx_AC_I`, `Vdc_Rx`, `I_Load`.

### Mixed Naming Systems

Failure: The same model mixes `Tx/Rx`, `Primary/Secondary`, `P-side/S-side`, and `原边/副边` for the same concept.

Correction: Choose one naming system and apply it consistently. Preserve an existing model convention if it is coherent.

### Library Family Mixed Incorrectly

Failure: Simscape physical-network blocks and Specialized Power Systems blocks are connected casually in the same power path.

Correction: Confirm the intended library family. Use appropriate interface blocks only when required and technically valid. Follow the reference model style when one is provided.

### Parameter Initialization Fragile

Failure: Model only runs after manual workspace setup, or parameters are split across stale scripts and callbacks.

Correction: Move small stable parameter sets to `InitFcn`, or use one clearly named initialization script. Remove unused scripts and duplicate definitions.

### LCC Branch Order Assumed

Failure: An LCC network is generated by convention even though the specific LCC variant was not confirmed.

Correction: Ask for the circuit diagram or confirm exact LCC order. Do not assume branch order for LCC/LCL/CLC networks.

### Multi-Receiver Outputs Accidentally Tied

Failure: `Rx1` and `Rx2` outputs share a DC bus when independent outputs were intended, or vice versa.

Correction: Confirm output structure. Draw independent outputs separately or draw common bus clearly with branch currents labeled.

### Final Model Contains Debug Leftovers

Failure: Temporary constants, manual switches, debug displays, old scopes, unused Goto/From tags, or abandoned blocks remain.

Correction: Remove them, or move them into a clearly named `Diagnostics` or `Scenario` subsystem only if still needed.
