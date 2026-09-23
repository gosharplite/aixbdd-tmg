# ADR 0006 — Claim→witness obligation for normative clauses and truth prose

- **Status:** Accepted
- **Date:** 2026-09-23
- **Deciders:** repo owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [aixbdd-tmg#15](https://github.com/gosharplite/aixbdd-tmg/issues/15) (defect & grill round);
  consumer evidence: `tellme` rounds 075, 080, 081 (`-b` rollback / ADR 0053);
  ADR 0001 (atomicity fold/split criterion);
  ADR 0004 (pre-delivery orphan coverage sweep);
  `skills/axb-specify/templates/spec.template.md`;
  `skills/axb-specify/rules/spec完整性與一致性自檢判準.md`;
  `skills/axb-tasks/templates/tasks.md`;
  `skills/axb-tasks/rules/Pre-Delivery覆蓋檢驗與孤立產物盤點判準.md`;
  `skills/axb-implement/rules/完成定義-驗證與回寫判準.md`;
  `skills/axb-bdd/rules/red-失敗訊號建立判準.md`;
  `skills/axb-technical-research/rules/techstack-artifact最小必要資訊判準.md`;
  `skills/axb-gherkin-and-dsl/STANDARDS.md`;
  `skills/axb-dsl-refine/rules/介面Gherkin原子化與單一Act判準.md`;
  `skills/axb-constitution/rules/目標檔定位與規則落點判準.md`;
  `domain-model/aixbdd.modelith.yaml`.

## Context

The AIxBDD pipeline successfully converts requirements into executable truth for observable behavior
via Gherkin `Rule`/`Example`, DSL rows, and step definitions (`dsl-exact-one-match`, topology audit).
However, non-observable claims — NFRs, crash-safety, durability, atomicity, concurrency guarantees,
and internal invariants (e.g., "performs no network access") — previously had no carrier across the
pipeline.

In consumer project `tellme` (round 081 / `-b` rollback / ADR 0053), the system asserted on multiple
live surfaces that rollback was atomic and durable (`temp file + fsync + atomic rename`). Mutation
testing revealed that while the temp-file and rename parts were tested, removing `f.Sync()` or directory
syncing left the entire test suite completely green. Two of the three mechanisms asserted with `MUST`
in the specification had no discriminating test input that could fail.

Audit across skills revealed:
1. `axb-specify` lacked per-`FR`/`NFR` verification slots;
2. `axb-tasks` swept only artifact consumption (ADR 0004), never falsifiability;
3. `axb-implement` equated "verified" with "test passes" (`green ≠ falsifiable`);
4. `axb-bdd` falsifiability rules were scoped exclusively to Gherkin interface slices;
5. `axb-technical-research` permitted behaviour guarantees to be misfiled as `techstack.md` rows;
6. `dsl.md` prologues and truth prose could state arbitrary unverified system invariants.

## Problem

A normative claim (`FR`, `NFR`, `SC`, `EC`, `MUST`) could reach truth surfaces as binding prose
without an executable witness that could fail. Once recorded on surfaces engineers trust, subsequent
rounds reason from these assertions as established facts, allowing silent behavioral drift and
regressions that no automated test can catch.

## Decision

Establish a mandatory **Claim→Witness Obligation** across the pipeline:
**No normative claim may reach truth unwitnessed.** Every normative clause must be carried by an
executable falsifier (a test that can go red) or be explicitly recorded as `accepted-unwitnessed` on a
project-declared decision surface.

### 1. Specification (PM side: coarse verification intent & explicit IDs)
- `spec.template.md`: Every `FR`, `NFR`, `SC`, and `EC` (minted with explicit `EC-nnn` IDs) carries a
  coarse-grained verification intent:
  - `observable → <acceptance Rule/Scenario>`
  - `unobservable → <witness tier>` (e.g. `unit pin`, `fault-injection seam`)
  - `accepted-unwitnessed` (project decision surface)
- `spec完整性與一致性自檢判準.md`: Added Rule 5 requiring all normative clauses to have verification intent.
  Per `spec-pm-authored`, the PM provides this coarse hint; RD decomposes it into atomic effect claims.

### 2. Execution Planning (RD side: atomic effect claims, [WITNESS] marker, and ledger)
- Enumerate by surface-anchored text walk across inputs and truth files (`techstack.md`, `dsl.md` prologue,
  DBML notes). The unit is the **Atomic Effect Claim**, not raw mechanism lines.
- Exclude mechanism statements unless the means is the contract (e.g. "performs no network access").
- Effect-strength calibration: guarantees must not exceed what is witnessed (e.g. do not claim power-loss
  durability when only in-process crash safety is tested).
- New `[WITNESS]` task marker in `tasks.md` with explicit `Dependencies: T###`, `Test Scope` (unit/fault-injection),
  and `Falsifier` (the discriminating mutation).
- Added `Pre-Delivery Claim→Witness Coverage Sweep` (Rule 3 in `Pre-Delivery覆蓋檢驗與孤立產物盤點判準.md`),
  emitting a `Claim→Witness 盤點對照表 (Claim→Witness Ledger)` in `tasks.md`.
- Anti-laundering clause: `accepted-unwitnessed` requires reason + proof why no input can falsify + tier
  ladder examination (`effect/fault-injection` → `mechanism-seam` → `accepted-unwitnessed`) + approval
  by a human decider + complete removal of prose from all truth surfaces.

### 3. Implementation (DoD three-gate check & demotion terminal outcome)
- `完成定義-驗證與回寫判準.md`: Added Rule 5 for `[WITNESS]` tasks:
  1. **Discriminating mutation**: A specific mutation breaking the guarantee causes the test to fail.
  2. **Attributed failure**: Failure is specifically attributable to the broken guarantee (failure shape matches).
  3. **Revert-and-re-green**: Reverting the mutation restores green status.
- Demotion: If a claim cannot be witnessed, the only alternative is to completely purge the prose guarantee
  from all truth surfaces and record it on the project decision surface. Faking completion is forbidden.

### 4. Failure Reality & Authority Split (`axb-bdd`)
- `red-失敗訊號建立判準.md`: Added Rule 5 extending the valid failing signal principle to non-Gherkin witness
  pins, establishing a clear two-authority split:
  - `axb-bdd` governs signal-reality, narrowest entry, and failure attribution;
  - `axb-implement` governs task binding, mutation verification, revert-and-re-green, and demotion.

### 5. Truth Scoping (`techstack.md` & `dsl.md` prologue)
- `techstack-artifact最小必要資訊判準.md`: Added Rule 6 prohibiting behaviour guarantees or invariants from
  being listed as techstack items.
- `STANDARDS.md` §2.1 & §7: Prohibited unverified behaviour guarantees in `dsl.md` prologues; added claim
  routing checks so unobservable NFRs are never dropped during DSL refinement.
- `介面Gherkin原子化與單一Act判準.md`: Rule 6 updated to mandate routing unobservable NFRs to `[WITNESS]` tasks
  or project decision surfaces.

### 6. Constitution & Decision Surface Declaration
- `目標檔定位與規則落點判準.md`: Added Rule 4 requiring the shared constitution (`.agents/constitution/shared.md`)
  to govern the Claim→Witness obligation and declare the project's decision surface (e.g. `decisions/NNNN-*.md`).
  If undeclared, `accepted-unwitnessed` blocks conditionally.

### 7. Domain Model
- `domain-model/aixbdd.modelith.yaml`: Updated `Task` definition and `Task.attributes.marker` description
  to include `witness` in the illustrative marker list. `marker` remains an open `string`.

## Alternatives considered

1. **Rely on code coverage tooling**: Rejected. Coverage measures execution, not discriminating force.
   Code executing `f.Sync()` will show 100% line coverage even when no assertion verifies that `f.Sync()`
   was called or succeeded.
2. **Mandate universal automated mutation testing frameworks**: Rejected. Full-suite mutation testing is
   costly and flaky; the obligation to identify the discriminating mutation per claim delivers the required
   rigor cheaply and reliably.
3. **Add a synthetic schema field to `TruthArtifact`**: Rejected. Truth files have heterogeneous formats
   (DBML, OpenAPI YAML, Markdown, Gherkin). An owner-skill rule and `tasks.md` ledger manage governance
   without contorting non-Markdown schemas.
4. **Treat `[WITNESS]` as a separate sibling skill**: Rejected. Witness pins are unit-tier or fault-injection
   tests executed within `axb-implement` alongside standard tasks; creating an extra skill adds orchestration
   overhead with no governance benefit.

## Consequences

- Normative clauses cannot silently reach truth without executable falsifiers or human-approved decision records.
- PMs explicitly communicate verification intent; RDs formally enumerate atomic effect claims in `tasks.md`.
- `[WITNESS]` tasks must be proven with discriminating mutations before marking `[X]`.
- Truth prose on `techstack.md` and `dsl.md` prologues is kept strictly within verified bounds.
- Domain model updated and re-rendered via `modelith render`.

## Calibration / acceptance

| Scenario | Check Behavior | Expected Outcome |
|---|---|---|
| Unobservable `MUST` (e.g. fsync durability) in `spec.md` | Checked in `tasks.md` Claim→Witness sweep | **Compliant** — mapped to `[WITNESS]` task with discriminating mutation. |
| Behaviour guarantee written directly as `techstack.md` row | Checked against Rule 6 of techstack rule | **Violation** — rejected; must route to executable truth or ADR. |
| Behaviour guarantee stated in `dsl.md` prologue without witness | Checked against `STANDARDS.md` §2.1 | **Violation** — rejected; must be witnessed via `[WITNESS]` or recorded on decision surface. |
| `[WITNESS]` task passes, but removing key mechanism leaves suite green | Checked against Rule 5 of implement DoD | **Violation** — non-discriminating; task completion rejected. |
| Unwitnessed claim labeled `accepted-unwitnessed` without reason or ladder | Checked against anti-laundering clause | **Violation** — rejected; requires ladder exhaustion and human decider. |
| Unwitnessed claim demoted from truth | Demotion verified | **Compliant** — prose completely purged from truth surfaces, documented in ADR. |
