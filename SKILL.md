---
name: wpt-simulink-model-building
description: Use when building, organizing, or reviewing Simulink or Simscape Electrical models for wireless power transfer (WPT) and bidirectional wireless power transfer (BWPT) systems. Focus on pre-build design alignment, topology clarity, explicit power-device modeling, compensation-network placement, coupled-coil representation, measurement placement, auxiliary subsystem organization, parameter initialization, layout aesthetics, and validation readiness. Do not use for synchronization-theory derivations, PQ-method derivations, patent theory, or control-algorithm proof work.
---

# WPT Simulink Model Building

Use this skill to build, organize, or review WPT/BWPT Simulink and Simscape Electrical models so the model is electrically clear, visually orderly, and ready for simulation validation.

This skill is about model construction quality only. It does not derive control algorithms, synchronization principles, PQ formulas, compensation-control theory, patent claims, or paper theory.

## Core Principle

First align the design, then build the model. Keep the physical WPT power topology explicit. Encapsulate auxiliary logic such as control, PWM, gate driver, protection, signal conditioning, diagnostics, and test scenarios into clean functional subsystems with meaningful ports.

## Required Workflow

1. Run pre-build design alignment before creating or restructuring a model.
2. Inspect a reference model first when one is provided, and preserve its library family unless the user asks to change it.
3. Classify the topology: branch count, power direction, compensation type, receiver conversion type, and model abstraction level.
4. Select a matching template only if it fits the confirmed system.
5. Build or organize the main power topology before controller internals.
6. Add measurements close to measured objects.
7. Encapsulate auxiliary modules into functional subsystems.
8. Configure parameters and callbacks in one reproducible place.
9. Clean layout after electrical connectivity is correct.
10. Report validation state honestly: opened, updated, compiled, simulated, waveform_checked, layout_checked.

## Pre-Build Design Alignment

Before formal model construction, ask targeted questions in small rounds. Do not silently assume choices that affect topology, device selection, library family, or enabled modules.

Minimum first round:

```text
1. What is the model purpose: topology display, control validation, device-level simulation, or final engineering model?
2. Is the system unidirectional WPT or bidirectional BWPT?
3. Is it single-Tx/single-Rx, single-Tx/multi-Rx, multi-Tx/single-Rx, or multi-Tx/multi-Rx?
4. What compensation topology is used: SS, LCC-S, LCC-LCC, full-series multi-branch, or another topology?
5. What is the receiver conversion type: diode rectifier, active rectifier, synchronous rectifier, DC/DC post-stage, or AC load?
6. Which modeling technology should be used: Simscape Electrical, Specialized Power Systems, or a provided reference model style?
```

Then confirm:

```text
power-stage device level
exact library/block choices when relevant
enabled modules: control, PWM, gate driver, dead time, protection, signal conditioning, scenario, diagnostics
required voltage/current measurements
Scope/logging requirements
naming language and layout preference
```

Before building, summarize confirmed choices and unresolved items. If the user asks to proceed with best judgment, state assumptions explicitly.

## Topology And Device Rules

Use `references/model-building-rules.md` for detailed rules on:

- topology classes and functional module placement
- device abstraction levels
- full bridges, diode rectifiers, active bridges, compensation elements, and coupled coils
- measurement and observation rules
- control, driver, protection, diagnostics, and scenario subsystems
- parameter and callback management
- layout and routing standards

Essential hard rules:

- Do not hide the main WPT/BWPT power path inside one large subsystem.
- Do not force every WPT model into a single-Tx/single-Rx dual-active-bridge layout.
- Do not assume receiver conversion type, coupling structure, or LCC branch order.
- Do not mix Simscape physical-network and Specialized Power Systems blocks casually in one power path.
- Do not leave switch gates floating in switch-level models.
- Do not keep final subsystem ports named `In1` or `Out1`.
- Do not leave debug scopes, stale constants, floating ports, or invalid wires in final models.

## Template Selection

Use `references/topology-templates.md` when the model matches one of these templates:

- Template A: SS unidirectional WPT.
- Template B: LCC-LCC bidirectional BWPT.
- Template C: 2-to-2 full-series-compensated WPT.

Do not force a template if it does not fit the confirmed topology.

## Review And Failure Correction

Use `references/review-and-failures.md` when reviewing, cleaning, or finalizing a model. It contains the completion checklist and common failure modes with corrections.

## Reporting

When reporting work on a WPT/BWPT model, include:

```text
model purpose
topology class
power direction
compensation topology
converter/rectifier types
library family
device abstraction level
enabled auxiliary subsystems
measurement outputs
layout state
validation state
remaining risks or assumptions
```

Keep the report about model construction and validation readiness, not theory derivation.
