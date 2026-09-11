# ADR 0003 — ParallelHint concurrency arbitration and disjoint file isolation

- **Status:** Accepted
- **Date:** 2026-09-11
- **Deciders:** `aixbdd-tmg` owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [aixbdd-tmg#9](https://github.com/gosharplite/aixbdd-tmg/issues/9) (defect),
  [tellme#5](https://github.com/gosharplite/tellme/issues/5) (grill round that surfaced it),
  `skills/axb-implement/rules/ParallelHint平行Subagent與衝突Merge判準.md` (Rule 1, Rule 2, Rule 3),
  `skills/axb-implement/rules/平行執行與檔案衝突判準.md` (Rule 2),
  `skills/axb-tasks/rules/TruthDelta影響盤點與任務型態判準.md` (Rule 5),
  `skills/axb-tasks/SKILL.md`,
  `skills/axb-implement/SKILL.md`.

## Context

In the `tellme` consuming project's Round-001 review ([tellme#5](https://github.com/gosharplite/tellme/issues/5)),
Phase 3 scheduled 45 DSL step definitions under a `Parallel Hint`.
During execution planning, a direct conflict was uncovered between concurrent subagent dispatch
and file-sharing merge expectations across `/axb-implement` rules:
1. `skills/axb-implement/rules/ParallelHint平行Subagent與衝突Merge判準.md` Rule 1 mandated dispatching all
   subagents simultaneously: *"當前 phase 有 Parallel Hint 時必須一次派出該批 subagent … 每個列出的 `[P]` task 派一個獨立 subagent"*.
2. Rule 3 of the same file and `skills/axb-implement/rules/平行執行與檔案衝突判準.md` Rule 2 stated:
   *"Phase 3 的 `[P]` 批次即使寫同一檔，仍各派 subagent；後寫入者先讀最新檔再 merge"*.
3. `skills/axb-implement/rules/平行執行與檔案衝突判準.md` Rule 2's Bad Example explicitly penalized serializing
   same-file tasks (*"看到 Parallel Hint 要 T008–T011 平行，但因同檔改成一個一個做"*).

This created an impossible operational contradiction for orchestrators and subagents when multiple tasks target the same physical file.

## Problem

- **Uncoordinated Concurrency vs. Lost Updates**: "一次派出該批" dispatches all subagents into concurrent execution.
  If two or more subagents write to the same physical file simultaneously, a classic Time-of-Check to Time-of-Use
  (TOCTOU) race condition occurs: both subagents read version $v_0$ of the file, generate code independently, and write
  back, resulting in lost updates where earlier writes are silently obliterated.
- **Fictitious Sequential Ordering ("後寫入者")**: The rule assumed a deterministic write order (*"後寫入者先讀最新檔再 merge"*),
  but without an external lock, file queue, or sequential scheduler, subagents have no way of knowing who is earlier or later,
  or when peer subagents have flushed their changes to disk.
- **Unreliable In-Prompt 3-Way Merging**: Instructing an LLM subagent in prompt to dynamically re-read and merge code
  without AST-level merge tooling frequently leads to hallucinations and syntax corruption.
- **Prohibiting the Safe Fallback**: By classifying intra-file serialization as a "Bad Example", the rules forbade
  orchestrators from taking the only safe concurrency-control measure when tasks share a file.

## Decision

Adopt a two-pronged solution combining **upstream architectural prevention** with **downstream execution arbitration**:

1. **Upstream (`axb-tasks`) — Zero Shared Edits Principle**:
   - In Phase 2 (`Foundational`) and Phase 3 (`Test Alignment`), task planning should prioritize independent, atomic landing
     files for each step definition (e.g. 1 task = 1 file, utilizing mechanisms such as Go `init()` auto-registration,
     pytest-bdd modular step files, or Cucumber step isolation) wherever the project language/framework permits.
   - When each task targets an independent file, write sets are disjoint ($\text{WriteSet}(T_i) \cap \text{WriteSet}(T_j) = \emptyset$),
     enabling true contention-free parallel dispatch.
2. **Downstream (`axb-implement`) — Disjoint Dispatch vs. Intra-File Serialization**:
   - **Disjoint targets (`Zero Shared Edits`)**: When tasks in a `Parallel Hint` batch write to disjoint files, the orchestrator
     MUST dispatch all subagents concurrently in parallel.
   - **Shared target files**: When multiple `[P]` tasks target the same physical file, simultaneous uncoordinated dispatch
     is strictly prohibited. The orchestrator MUST serialize execution for tasks sharing that file (either running them
     sequentially so the subsequent agent reads the flushed prior write, or grouping same-file tasks into a single subagent).
   - **Review task role**: The Phase 3 review task remains audit-only (verifying that no undefined steps remain and all tests compile),
     not a diff/patch merger.
   - **Rule 2 Bad Example revised**: Serializing tasks that share a physical file is recognized as necessary concurrency safety;
     penalties apply only when an orchestrator needlessly serializes tasks whose target files are already independent.

## Alternatives considered

1. **In-prompt re-read and merge only (Status Quo)**: Rejected. Concurrent subagents writing to shared files without locks
   cause lost updates and race conditions. Prompt-based 3-way merges without AST tooling corrupt syntax.
2. **Mandate Zero Shared Edits unconditionally without downstream arbitration**: Rejected. Some legacy projects, languages,
   or team conventions do not support 1-file-per-stepdef. Downstream execution arbitration is still required when files are shared.
3. **Completely eliminate Parallel Hint (serialize all tasks)**: Rejected. For large test suites (e.g. 45 stepdefs in `tellme`),
   parallel execution with isolated files yields massive throughput gains. We should preserve and maximize safe parallelism.

## Consequences

- `skills/axb-tasks/rules/TruthDelta影響盤點與任務型態判準.md`: Added Rule 5 recommending independent landing files
  (Zero Shared Edits) for Phase 2 and Phase 3.
- `skills/axb-tasks/SKILL.md`: Mentioned Zero Shared Edits in Phase 2 Foundational and Phase 3 Test Alignment planning.
- `skills/axb-tasks/templates/tasks.md` and `tasks.example.md`: Updated Phase 3 Boundary and Parallel Hint annotations.
- `skills/axb-implement/rules/ParallelHint平行Subagent與衝突Merge判準.md`:
  - Rule 1 updated: Parallel dispatch applies to disjoint files; shared files require serialization/grouping.
  - Rule 2 updated: Clarified prompt instruction for sequential execution within shared files.
  - Rule 3 updated: Prohibited simultaneous writes to shared files; affirmed review is audit-only.
- `skills/axb-implement/rules/平行執行與檔案衝突判準.md`:
  - Rule 2 updated: Shared files must be serialized; Parallel Hint applies concurrently to independent files.
  - Bad Example updated: Distinguishes between safe intra-file serialization and unnecessary serialization of disjoint files.
- `skills/axb-implement/SKILL.md`: Operating Principles and Phase 4 aligned with the new arbitration logic.
- `decisions/README.md`: Index updated with ADR 0003.
- **Domain model**: No change required. The `Task` invariant `task-strict-ordering` (completing and verifying the batch
  before subsequent tasks start) remains fully satisfied.
- **Consuming projects**: Projects using 1-file-per-stepdef (`Zero Shared Edits`, as adopted by `tellme`) can dispatch full
  parallel batches safely. Projects with shared stepdef files are protected against lost updates via orchestrator serialization.

## Calibration / acceptance

| Scenario | Dispatch Behavior | Expected Outcome |
|---|---|---|
| T008 writes `step_a.go`, T009 writes `step_b.go` (disjoint files) | Concurrent Parallel Dispatch | **Compliant** — all subagents dispatched at once. |
| T008 and T009 both write `steps/chat.py` (shared file) | Intra-file Serialization | **Compliant** — T008 completes and flushes, then T009 reads latest file; no lost updates. |
| T008 and T009 both write `steps/chat.py` | Dispatched concurrently at once | **Violation** — forbidden race condition causing lost updates. |
| T008 and T009 write disjoint files | Serialized one-by-one despite Parallel Hint | **Violation** — unnecessary serialization of independent tasks. |
| T015 review task | Audits undefined steps & test compilation | **Compliant** — does not merge diffs or write code. |
