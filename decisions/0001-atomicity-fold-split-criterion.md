# ADR 0001 — Atomicity fold/split criterion (Rule 2)

- **Status:** Accepted
- **Date:** 2026-09-11
- **Deciders:** `aixbdd-tmg` owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [aixbdd-tmg#5](https://github.com/gosharplite/aixbdd-tmg/issues/5) (defect),
  [tellme#4](https://github.com/gosharplite/tellme/issues/4) (grill round that surfaced it),
  `skills/axb-dsl-refine/rules/介面Gherkin原子化與單一Act判準.md` Rule 2 & Rule 7,
  `skills/axb-gherkin-and-dsl/STANDARDS.md` §2,
  `domain-model/aixbdd.modelith.yaml` invariant `acceptance-atomic-rules`.

## Context

`/axb-dsl-refine` is the host of the interface-atomicity rule ("one Rule = one independently
verifiable aspect"). A consuming project (`tellme`) escalates rule defects to this repo via
`/axb-clarify` → issue → PR, but until now the repo had **no decision-record mechanism** for its own
governed artifacts, so the *reasoning* behind a rule fix had nowhere to live. That is the second,
structural gap addressed here (see the new `decisions/` README); #5 is the first concrete case.

## Problem

Rule 2 supplied four statements with **no precedence and no operative test**:

- a **falsifier** ("若某個預期結果可以在另一個結果仍成立時單獨失敗 … 必須拆成不同 Rule") that splits;
- a **fold** clause ("多個 Then / And 只有在共同證明同一業務事實時才可留在同一 Rule") that folds,
  with no definition of "同一業務事實";
- a **title smell** ("標題若同時包含成功與失敗 … 通常表示仍可再拆"); and
- a **check question** ("只改壞其中一個結果時，這個 Rule 是否仍可能通過？") that merely restates the
  falsifier in its *bug-injection* reading.

`STANDARDS.md` §5.2's channel taxonomy (`呈現結果` / `權威狀態` / `再讀確認` / `不該發生` …) says *how to
verify a fact*, not *how many facts a Rule may carry*, so it cannot arbitrate.

Two structurally **isomorphic** cases in `tellme` round-001 received **opposite** verdicts (W2 =
container + contained file; D2 = a resolved configuration + its sub-resolutions). Because the cases
are isomorphic, any consistent criterion must return the *same* verdict for both — the observed
opposite verdicts are therefore **not reproducible** from the rule. `tellme#4` Q3 concluded the
fold/split boundary is "not derivable from Rule 2" and reframed it as this governance gap.

## Decision

Rule 2 now states a single operative criterion:

> A `Rule` carries **exactly one named subject** (one business event/outcome). Additional
> `Then` / `And` fold in **iff** they are **entailed** by that subject's outcome — i.e. *no valid domain
> state exists where the outcome holds and the assertion fails*. Split when an assertion introduces a
> **second subject**, or a fact the outcome does not entail. The falsifier is read over **valid domain
> states**, never over injected bugs (any bug can fail any single assertion — that reading degenerates
> to one `Then` per Rule). The §5.2 channels (and the exit/verdict channel) are the *vocabulary* for
> the one subject's evidence, not a licence to fold un-entailed facts. The title heuristic is demoted
> to a **smell**, not the test.

The criterion is paired with a **calibration set** (Rule 2) that any candidate criterion must
reproduce.

## Alternatives considered

1. **One-subject / "channels fold in" reading** (the reading `tellme` round #4 landed on). Rejected:
   it folds the §5.2 channels into the subject unconditionally, which collapses
   Rule 6's Good Example — three *independently-failable* prohibitions for one out-of-turn guess
   (「不得送出 / 不得新增歷史 / 不得改變回合」) would merge into one Rule, contradicting Rule 6. It also
   does not define "subject" without "independently-failable", i.e. circularly.
2. **Keep the falsifier, declare it necessary-but-not-sufficient, let the same-fact clause control.**
   Rejected: "same business fact" stays undefined; no calibration set; no reproducible verdict.
3. **Title heuristic only.** Rejected: it is a smell, not a decision procedure, and it does not
   distinguish the isomorphic W2/D2 pair.

The **entailment** reading is the only one that keeps Rule 1 (three independent三連猜 results → split),
Rule 2's Good Example (獲勝 ⟹ 對局結束 → fold), and Rule 6 (independent prohibitions → split) all valid
simultaneously.

## Consequences

- `skills/axb-dsl-refine/rules/介面Gherkin原子化與單一Act判準.md`: Rule 2 rewritten (criterion +
  calibration set); Rule 7's checklist bullet now references the entailment test.
- `skills/axb-gherkin-and-dsl/STANDARDS.md` §2 now points at the single operative criterion instead of
  restating the title heuristic.
- `domain-model/aixbdd.modelith.yaml`: invariant `acceptance-atomic-rules` restated; model re-rendered.
- `skills/axb-spec-by-example/rules/gherkin-驗收句型與結構判準.md` Rule 4: the title test is demoted to
  a smell so the acceptance layer matches the restated invariant.
- **Cross-owner note:** this PR intentionally edits artifacts owned by two skills
  (`axb-dsl-refine`, `axb-gherkin-and-dsl`) plus the canonical model; the PR itself is the human
  review gate that the per-round skills cannot provide for a host-rule change.
- **Migration:** a consuming project that folded an un-entailed pair (e.g. `tellme` W2 / D2) must now
  re-decide it against its domain definitions. Where the pair is genuinely entailed (獲勝/對局結束,
  create+report+exit) no change is needed; where it is not, the pair is split.

## Calibration / acceptance

A candidate criterion is accepted only if it reproduces **all** of:

| Case | Expected |
| --- | --- |
| Rule 2 Good Example — `對局已結束` + `Alice 獲勝` | **fold** (win entails game-over) |
| CLI successful create — response + exit 0 | **fold** (success decides the response/verdict channel) |
| Rule 1 Good Example — three三連猜 Rules | **split** (independent results) |
| Rule 6 Good Example — 拒絕 / 不新增歷史 / 不改變回合 | **split** (independent prohibitions) |
| `tellme#4` W2 — reuse workspace + still holds file | see domain definition of "reuse" |
| `tellme#4` D2 — resolved + sub-resolutions | see domain definition of "resolved" |
| W2 vs D2 | **identical verdict** (structurally isomorphic) |
