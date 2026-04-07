---
name: project-intelligence
description: Structured software-project analysis skill for unfamiliar repositories, pull requests, git diffs, business-logic extraction, workflow reconstruction, state/data-flow mapping, architecture walkthroughs, onboarding guides, regression-risk review, refactor assessment, and durable knowledge extraction. Use when Claude Code, Codex, or OpenClaw needs to understand a codebase in project context, reconstruct business modules from front-end or back-end code, generate a Project Profile, run review/risk/memory/doc/onboarding/domain/refactor analysis modes, or orchestrate a multi-step repo analysis workflow.
---

# Project Intelligence

Use this skill to analyze software projects in a structured, profile-aware way.

## Core workflow

1. Identify the user goal: `review`, `risk`, `memory`, `doc`, `onboarding`, `domain`, `refactor`, or broad project understanding.
2. Check whether a Project Profile already exists.
3. If no suitable profile exists, generate or draft one first.
4. Load the matching mode instructions and only the resources needed for that request.
5. Produce structured output, and state assumptions and open questions instead of guessing.

## Resource map

Read these files directly when needed:

- `references/agent-guide.md`: quick selection guide, expected inputs, and execution patterns
- `prompts/system/orchestrator.md`: multi-step planning for broad or ambiguous requests
- `prompts/system/core-analyzer.md`: shared analysis engine used by all modes
- `prompts/system/profile-generator.md`: create or refine a Project Profile from repo context
- `prompts/system/knowledge-merger.md`: merge newly extracted knowledge with existing entries
- `modes/review.md`: project-aware code review
- `modes/risk.md`: regression risk and validation planning
- `modes/memory.md`: extract durable project knowledge
- `modes/doc.md`: generate technical documentation drafts
- `modes/onboarding.md`: produce newcomer guides
- `modes/domain.md`: reconstruct business modules, workflows, state changes, and data relationships from code
- `modes/refactor.md`: identify refactor candidates and tradeoffs
- `schemas/project-profile.schema.json`: shape of project profiles
- `schemas/analysis-result.schema.json`: expected analysis output contract
- `schemas/knowledge-entry.schema.json`: shape of memory knowledge entries
- `profiles/*.sample.json`: starter project profile templates
- `ci/README.md`: CI integration patterns

## Mode selection

- Use `review` for PRs, diffs, or code changes that need findings and recommendations.
- Use `risk` when the user cares about regressions, blast radius, or validation scope.
- Use `memory` after analysis when the goal is to preserve project-specific knowledge.
- Use `doc` to draft technical docs from code or architecture context.
- Use `onboarding` for repo walkthroughs and newcomer reading paths.
- Use `domain` when the user cares about business modules, entrypoint-to-operation mapping, workflows, state transitions, integration boundaries, or core business logic.
- Use `refactor` to surface structural problems, opportunities, and safe next steps.
- Use `orchestrator` when the user asks for a broad analysis and the right sequence is not obvious.

## Inputs to gather

Prefer collecting only the minimum set needed:

- repo tree or key directories
- README or other high-level project docs
- diff / PR / changed files for change analysis
- representative source files or entrypoints
- existing Project Profile, if any
- test commands or architecture notes when risk analysis matters

## Output rules

- Follow `schemas/analysis-result.schema.json` for structured analysis output.
- Keep analysis grounded in repo evidence.
- Explicitly list assumptions, confidence, and open questions.
- Do not invent domain rules that are not supported by the code or profile.
- If creating knowledge entries, preserve only durable insights worth reusing.

## Notes

- `skill.yaml` is legacy project metadata for this repo. Triggering for formal skill loaders comes from this `SKILL.md` frontmatter.
- Keep `SKILL.md` concise. Put detailed guidance in referenced files.
