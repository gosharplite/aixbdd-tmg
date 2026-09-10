# ADR 0002 — Project-language override for STANDARDS (filenames, parameter keys, prose)

- **Status:** Accepted
- **Date:** 2026-09-11
- **Deciders:** `aixbdd-tmg` owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [aixbdd-tmg#6](https://github.com/gosharplite/aixbdd-tmg/issues/6) (defect),
  [tellme#4](https://github.com/gosharplite/tellme/issues/4) (grill round that surfaced it),
  [ADR 0001](0001-atomicity-fold-split-criterion.md),
  `skills/axb-gherkin-and-dsl/STANDARDS.md` (§2, §3, §4),
  `skills/axb-gherkin-and-dsl/scripts/audit_feature_dsl_topology.py`,
  `skills/axb-constitution/rules/目標檔定位與規則落點判準.md`.

## Context

`STANDARDS.md` is the **host** standard the `axb-gherkin-and-dsl` skill applies to a consumer
project's truth tree. Its §2 and §3 stated, **unconditionally**, that feature filenames and Gherkin
parameter keys use 繁體中文. A consuming project that authors artifacts in another language (`tellme`
ships English) therefore could not comply and shipped an **unratified deviation** — exactly the
`truth-single-owner` shape, transposed to the methodology layer: a project violating a host standard
with no warrant an owner can point at. The open question (`tellme#4` Q8) was the *scope*: does the
language rule reach filenames, parameter keys, the DSL meta-schema tokens, prose, or only some?

## Problem

- §2 *"檔名用繁體中文"* and §3 *"參數 key 名稱用繁體中文"* had **no override clause** anywhere.
- The DSL meta-schema tokens (§4/§5 headers, channel labels) are also Chinese, so it was unclear which
  surfaces are *contract* and which are *project prose*.
- The repo is **not** free of language provisions: `roles/pm.yaml` and `roles/rd.yaml` both end with
  *"Respond concisely and accurately in English."* (The issue's `**/*.md`-scoped grep missed these; it
  matched only `.md`, and none of those contain "English".) And
  `skills/axb-constitution/rules/目標檔定位與規則落點判準.md` already uses
  「說明文字必須使用繁體中文」as the canonical **`shared.md`** rule because *"語言一致性會被多種 artifact
  共同使用"* — i.e. the methodology already intends a language rule at the governance layer.
- **Decisive tooling fact:** `audit_feature_dsl_topology.py` requires the first DSL column header to be
  exactly `DSL 句型` (`if not header or header[0].strip() != "DSL 句型": continue`). Translating it makes
  the audit **silently skip the table**, so every step is reported as `找不到 DSL row`. The meta-schema
  tokens are therefore *tool-enforced*, not merely conventional.

## Decision

Add a **Project Language** clause to `STANDARDS.md`, with the scope stated precisely:

- Default artifact language is **繁體中文**, but language is **project-declared**, not fixed.
- A project MAY override it; the declaration must live in a **named home** — the project's
  `.agents/constitution/shared.md`, a project ADR (`decisions/NNNN-*.md`), or a `spec.md` explicit
  constraint — and **defaults to 繁體中文** when absent.
- Scope: **project-declared** = feature filenames (§2), Gherkin parameter keys (§3, coupled to the
  Gherkin language), Gherkin sentences / keywords / prose. **Fixed** = the §4/§5 DSL meta-schema tokens
  (`DSL 句型`, `Gherkin 參數`, `Data Table 參數`, `預設參數`, 實作語意欄) and the §5.2 channel labels /
  §5.1 Given-When sub-labels. §3's quoting/DataTable conventions are language-independent and unchanged.

`STANDARDS.md` §2, §3 and §4 now point at the clause.

## Alternatives considered

1. **Make §2/§3 "SHOULD" instead of an override clause.** Rejected: a `SHOULD` still leaves no warrant;
   the deviation stays unratified.
2. **Let projects translate the DSL meta-schema tokens too.** Rejected: breaks the mechanical audit
   (silent zero-match) and cross-project token consistency.
3. **Define a language-independent canonical token set now.** Rejected as over-scope for this decision;
   the fixed-token list is sufficient and removing it is a separate change.
4. **Do nothing; treat English as a project-local deviation.** Rejected: the host standard would remain
   silently violated, which is the defect.

## Consequences

- `skills/axb-gherkin-and-dsl/STANDARDS.md`: new **Project Language** section; §2/§3 become defaults;
  §4 points at the clause.
- **Reconciliation:** complements the governance-layer language rule
  (`.agents/constitution/shared.md`「說明文字必須使用繁體中文」) — that rule sets the default language and
  its home; this clause sets the **overridable scope**.
- **No domain-model change:** the model's `acceptance-business-language` invariant concerns
  *business vs technical* language, not natural language, so nothing there needs to change.
- **Migration:** a non-Chinese consumer (e.g. `tellme`) that recorded the language as an unratified
  residual can now cite this clause + a named declaration home as its warrant. Genuinely Chinese
  projects are unaffected (default unchanged).
- **Follow-up:** a project that overrides the language should record the declaration in the named home,
  not only in a status log.

## Calibration / acceptance

- A project that declares English: filenames, parameter keys, and prose are English → **compliant**.
- The same project keeps `DSL 句型 | Gherkin 參數 | Data Table 參數 | 預設參數 | 實作語意` and the §5.2
  channel labels unchanged → **compliant** (and the audit still passes).
- A project with **no** declaration → default 繁體中文 → §2/§3 apply as before.
