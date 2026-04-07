# Agent Guide

This file is the lightweight operating guide for agents using `project-intelligence` as a formal skill.

## Choose the right path

### 1. The repo or project is unfamiliar
- Read the root README and inspect the repo tree.
- If no Project Profile exists, use `prompts/system/profile-generator.md` plus `schemas/project-profile.schema.json`.
- For broad requests like "understand this repo" or "analyze this PR from all angles", use `prompts/system/orchestrator.md`.

### 2. The user asks for review of a diff or PR
- Load `prompts/system/core-analyzer.md`.
- Load `modes/review.md`.
- Optionally load `modes/risk.md` when regression scope matters.
- Return structured findings with evidence, severity, suggested tests, assumptions, and open questions.

### 3. The user asks about regressions, blast radius, or test planning
- Load `prompts/system/core-analyzer.md`.
- Load `modes/risk.md`.
- Use the Project Profile if present so module mapping and risk patterns stay project-aware.

### 4. The user wants durable project memory
- Run the relevant analysis first.
- Then load `modes/memory.md`.
- If knowledge should be merged into an existing store, load `prompts/system/knowledge-merger.md` and `schemas/knowledge-entry.schema.json`.

### 5. The user wants docs or onboarding help
- For docs, load `modes/doc.md`.
- For newcomer guidance, load `modes/onboarding.md`.
- Bring in the README, entrypoints, and config files before writing.

### 6. The user wants refactor planning
- Load `modes/refactor.md`.
- Pair with `modes/risk.md` when proposing real changes so the recommendation includes a safe rollout/testing view.

## Recommended working style

- Stay project-aware. Prefer profile-backed conclusions over generic advice.
- Stay honest. Use assumptions and open questions instead of pretending certainty.
- Stay structured. The value of this skill is consistent output shape, not just prose quality.
- Stay incremental. Draft a minimal profile first, then refine it from real repo evidence.

## Project Profile guidance

Use `profiles/*.sample.json` as starting points.

A minimal useful profile should at least try to capture:
- project name
- main modules
- important paths
- major technologies
- domain terms or risk patterns if they are clear

Do not overfit the profile before the repo is understood.

## Output contract

For analysis results, align with `schemas/analysis-result.schema.json`.

At minimum include:
- summary
- intent
- confidence
- affected_modules
- behavioral_observations
- risks
- suggested_tests
- assumptions
- open_questions
- mode_specific

## Current repo layout

- `prompts/system/`: reusable prompts and orchestration layers
- `modes/`: mode-specific overlays
- `schemas/`: JSON contracts for profiles, analysis, and knowledge
- `profiles/`: sample project profiles
- `knowledge/`: persisted project or global knowledge
- `ci/`: CI integration examples
- `skill.yaml`: legacy metadata for this repository
- `SKILL.md`: formal entrypoint for standard skill loaders
