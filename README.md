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

## Output Contract

Each generation should include:

1. Thinking Process
- `Narrative Gate Report`
- `Physics Gate Report`

2. Final Prompt
- English prompt in a code block.

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

## Verification Strategy

Use both regression files for quality checks:

- Narrative regression validates story coherence and single-event focus.
- Physics regression validates real-world consistency constraints.

A prompt should only be treated as fully compliant when both gates pass.
