# WPT Simulink Model Building

Codex skill for building, organizing, and reviewing Simulink or Simscape Electrical models for wireless power transfer (WPT) and bidirectional wireless power transfer (BWPT) systems.

## Purpose

This skill helps Codex construct WPT/BWPT models with clear power topology, explicit power-device choices, orderly measurement placement, clean auxiliary subsystems, reproducible parameter initialization, and readable Simulink layout.

It focuses on model-building discipline, not synchronization-theory derivation, PQ-method derivation, patent theory, or control-algorithm proof work.

## Contents

```text
wpt-simulink-model-building/
|-- SKILL.md
|-- agents/
|   `-- openai.yaml
`-- references/
    |-- model-building-rules.md
    |-- topology-templates.md
    `-- review-and-failures.md
```

## Main Capabilities

- Run pre-build design alignment before constructing a model.
- Classify WPT/BWPT topology by branch count, power direction, compensation network, receiver conversion type, and abstraction level.
- Keep the main power path explicit while allowing control, drive, protection, diagnostics, and scenario logic to live in clean subsystems.
- Apply model layout rules for readable, horizontal/vertical Simulink wiring.
- Use built-in topology templates for SS unidirectional WPT, LCC-LCC bidirectional BWPT, and 2-to-2 full-series-compensated WPT.
- Review models for connectivity, naming, parameter, measurement, and validation readiness issues.

## Installation

Place this folder under your Codex skills directory:

```text
C:\Users\Administrator\.codex\skills\wpt-simulink-model-building
```

For another machine, copy or clone the repository into the corresponding Codex skills directory.

## Usage

Ask Codex to use the skill when building or cleaning a WPT/BWPT Simulink model, for example:

```text
Use wpt-simulink-model-building to create an SS unidirectional WPT Simulink model.
```

```text
Use wpt-simulink-model-building to review and clean the layout of this BWPT Simscape model.
```

The skill will first confirm key modeling choices such as power direction, compensation topology, device abstraction level, library family, enabled auxiliary modules, measurement outputs, parameter handling, and layout preference.

## Validation

The skill folder has been validated with Codex's `quick_validate.py` skill validation script.
