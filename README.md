# Architectural Prompting Skill

This repository contains a production-oriented skill package for generating
architectural rendering prompts with both narrative coherence and physical
plausibility.

## Goal

Generate prompts that are:

- visually strong,
- physically consistent,
- and story-driven (not just keyword stacking).

## Core System

The prompt pipeline is built around two mandatory gates before final drafting:

1. Narrative Gate
- Build a Story Card: `Where / When / Who / What / Why / Mood / Evidence`.
- Detect narrative conflicts with `N0/N1/N2`.
- Compute `Narrative Score`.
- Block photorealistic storytelling claims if `N0 > 0`.

2. Physics Gate
- Check lighting, weather, materials, optics, structure, and behavior
  consistency.
- Detect physical conflicts with `P0/P1/P2`.
- Compute `Physics Score`.
- Block photorealistic claims if `P0 > 0`.

## Workflow Diagram

```mermaid
flowchart TD
    A["Receive user request"] --> B["Mandatory Check<br/>Lighting/Atmosphere + Core Material"]

    B -->|Missing any required field| C["Ask clarification only<br/>status=ASK_USER<br/>questions not empty"]
    C --> Z["Wait for user input"]

    B -->|Both provided by user| D["Narrative Intent Gate<br/>Story Card + N0/N1/N2 + Score"]
    D -->|N0 > 0 or score < 80| E["Fix narrative conflict or downgrade to conceptual mode<br/>status=REVISE or BLOCK"]

    D -->|N0 = 0 and score >= 80| F["Physics Consistency Gate<br/>P0/P1/P2 + Score"]
    F -->|P0 > 0 or score < 85| G["Report physical conflict and provide two options<br/>1) physically real version (recommended)<br/>2) stylized non-photoreal version<br/>status=REVISE or BLOCK"]

    F -->|P0 = 0 and score >= 85| H["Implicit Optimization<br/>environment-narrative coupling"]
    H --> I["Draft Prompt<br/>framework + corpus"]
    I --> J["Token Binding<br/>material_id / color_id + D5 + real-world"]
    J --> K["Schema Validation<br/>resources/schemas/apr-output.schema.json"]

    K -->|Pass| L["Output fixed JSON<br/>schema_version=apr-json-1.1<br/>status=READY<br/>questions=[]"]
    K -->|Fail| M["Fix JSON and validate again"]
```

## Output Contract

Each generation must return one fixed JSON object validated by:

- `resources/schemas/apr-output.schema.json`

Key constraints:

- All materials must resolve to canonical `material_id` values from
  `resources/catalogs/material_catalog.v1.json`.
- All colors must resolve to canonical `color_id` values from
  `resources/catalogs/color_catalog.v1.json`.
- Renderer mappings must come from
  `resources/catalogs/renderer_bindings.v1.json`.
- Current baseline renderer is D5 (`engine = d5`).
- Real-world specification mappings must come from
  `resources/catalogs/real_world_bindings.v1.json`.
- `status` must be one of: `ASK_USER`, `READY`, `REVISE`, `BLOCK`.

## Repository Layout

- `SKILL.md`: Main skill definition and execution requirements.
- `resources/framework.md`: Prompt framework template (`v1.5`).
- `resources/workflow.md`: End-to-end workflow (`v5.1`).
- `resources/corpus.md`: Lighting/material terminology corpus.
- `resources/categories.csv`: Quick category presets.
- `resources/integration_map.md`: Template-to-corpus mapping.
- `resources/narrative_rules.md`: Narrative rule engine and scoring.
- `resources/physics_rules.md`: Physics rule engine and scoring.
- `resources/narrative_regression_cases.md`: Narrative regression suite.
- `resources/physics_regression_cases.md`: Physics regression suite.
- `resources/catalogs/material_catalog.v1.json`: Canonical material token IDs.
- `resources/catalogs/color_catalog.v1.json`: Canonical color token IDs.
- `resources/catalogs/renderer_bindings.v1.json`: Material to renderer bindings.
- `resources/catalogs/real_world_bindings.v1.json`: Material to real-world specs.
- `resources/schemas/apr-output.schema.json`: JSON output validation schema.
- `resources/examples/apr-output.ready.example.json`: READY status JSON example.

## Verification Strategy

Use both regression files for quality checks:

- Narrative regression validates story coherence and single-event focus.
- Physics regression validates real-world consistency constraints.

A prompt should only be treated as fully compliant when both gates pass.
