# ADR 0004 — Pre-Delivery Orphan Coverage Sweep for tasks.md

- **Status:** Accepted
- **Date:** 2026-09-11
- **Deciders:** `aixbdd-tmg` owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [aixbdd-tmg#10](https://github.com/gosharplite/aixbdd-tmg/issues/10) (defect),
  [tellme#5](https://github.com/gosharplite/tellme/issues/5) (grill round that surfaced it),
  `skills/axb-tasks/SKILL.md` (Phase 5),
  `skills/axb-tasks/rules/Pre-Delivery覆蓋檢驗與孤立產物盤點判準.md`,
  `skills/axb-tasks/templates/tasks.md`.

## Context

In the `tellme` consuming project's Round-001 review ([tellme#5](https://github.com/gosharplite/tellme/issues/5)),
three independent disconnects between upstream specifications/decisions and generated tasks were identified (Orphaned Artifacts):
1. **`research.md` Decision 3 (6-step resolver algorithm)**: Defined core system resolution behavior, but no task in `tasks.md` had a `Read` pointing to it or mentioned it in `Boundary`.
2. **`techstack.md` Version assertion (`VERSION=0.0.0-harness`) & `research.md` Decision 6**: Explicitly defined the version string and build parameters, but no build task read that section or bound the compile flags.
3. **`research.md` Decision 7 (`verify-no-test-sleep`) & `techstack.md` declared `verify` target**: Adopted in technical research and declared in techstack truth, but the Makefile setup task (T002) omitted that target.

Because `/axb-implement` operates under a strict minimal-context rule (`技術參照載入與最小上下文判準.md`), subagents only read what is listed in task `Read` and phase `Shared Must Read`. Upstream decisions not linked in `tasks.md` are never loaded into downstream context and are silently lost during implementation.

## Problem

- **Verification Blind Spot**: `axb-tasks` Phase 5 previously only checked syntax and formatting (e.g. `- [ ] T###`, Core Inputs, no Impact Audit phase, Setup and Foundational do/don't clauses, Phase 3 markers, Feature phase markers, Test Scope).
- **Absence of Forward-Traceability**: There was no mechanical check verifying that upstream artifacts produced in earlier phases (`research.md` Decisions, non-NOOP rows in `truth-delta.md`, and updated `techstack.md` sections) were actually consumed by or mapped to tasks.
- **Silent Omission**: Planners could easily forget to link a researched decision or a truth modification to a task, resulting in orphaned artifacts that subagents never implemented.

## Decision

Introduce a mandatory **Pre-Delivery Orphan Coverage Sweep** in `axb-tasks` Phase 5, governed by a new rule file `skills/axb-tasks/rules/Pre-Delivery覆蓋檢驗與孤立產物盤點判準.md`:

1. **Pre-Delivery Orphan Coverage Sweep**:
   - Before delivering `tasks.md`, the planner must perform a forward-traceability sweep across all round inputs:
     - All non-NOOP rows in `truth-delta.md` (`ADD`, `MODIFY`, `DELETE`) must be 100% assigned to tasks.
     - All decided Decisions in `research.md` must be cited in at least one task's `Read` or directly delivered by a specific task (negative decisions constraining implementation must be stated in `Boundary` or `Read`).
     - All new or modified sections of `specs/truth/techstack.md` (build parameters, flags, versioning, test runners, make targets) must be bound to a task's `Read`.
   - **NOOP Exemption**: `truth-delta.md` `NOOP` entries indicate audited areas with no change required; they are explicitly exempt from requiring implementation tasks.
   - Any unreferenced, undelivered non-NOOP truth item or research decision blocks delivery until a task is added or the `Read` reference is supplemented.
2. **Task Binding Contract**:
   - `tasks.md` and `tasks.example.md` include this coverage sweep in their `Task Binding Contract`.

## Alternatives considered

1. **Rely on downstream implementer to scan `research.md` and `techstack.md`**: Rejected. Directly violates `axb-implement`'s minimal context policy (`技術參照載入與最小上下文判準.md`), which forbids speculative directory reading and unrestricted context inflation.
2. **Only check `truth-delta.md` and ignore `research.md` decisions**: Rejected. `tellme#5` specifically failed on algorithm (Decision 3) and build parameter (Decision 6) decisions that lived in `research.md`.
3. **Rely on human review to catch omissions**: Rejected. Manual inspection across 40+ tasks is error-prone. A mechanical sweep guarantees complete forward-traceability before hand-off.

## Consequences

- `skills/axb-tasks/rules/Pre-Delivery覆蓋檢驗與孤立產物盤點判準.md`: Created new rule file establishing Rule 1 (mandatory sweep) and Rule 2 (remediation mechanics).
- `skills/axb-tasks/SKILL.md`: Phase 5 step 2 updated to require the Pre-Delivery Orphan Coverage Sweep before formatting checks.
- `skills/axb-tasks/templates/tasks.md` and `tasks.example.md`: Added the Orphan Coverage Sweep requirement to `Task Binding Contract`.
- `decisions/README.md`: Index updated with ADR 0004.
- **Domain model**: No change required. The sweep enforces fidelity between `Research`, `TruthArtifact`, and `Task` within the existing `PlanPackage` relationship graph.

## Calibration / acceptance

| Scenario | Check Behavior | Expected Outcome |
|---|---|---|
| `research.md` Decision 3 (algorithm) cited in T018 `Read` | Verified in sweep | **Compliant** — decision has consumer task. |
| `research.md` Decision 3 omitted from all task `Read`s | Flagged as orphaned artifact | **Violation** — delivery blocked until `Read` added. |
| `techstack.md` VERSION flag cited in T001 `Read` | Verified in sweep | **Compliant** — compile parameter bound. |
| `techstack.md` `make verify` target omitted from tasks | Flagged as orphaned artifact | **Violation** — delivery blocked until task added. |
| `truth-delta.md` NOOP row | Verified as checked without change | **Compliant** — no implementation task required. |
| `truth-delta.md` ADD row has no task in Phase 3/4 | Flagged as missing coverage | **Violation** — delivery blocked until task created. |
