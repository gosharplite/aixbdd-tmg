# ADR 0005 — axb-ui-plan terminal mode for CLI/TUI interfaces

- **Status:** Proposed
- **Date:** 2026-09-13
- **Deciders:** repo owner (gosharplite)
- **Supersedes:** —
- **Related:** aixbdd-tmg#13; PR #2 (`InterfaceKind: cli`); `skills/axb-ui-plan/**`; `domain-model/aixbdd.modelith.yaml`; driver: tellme round 015 (`-i` Interactive TUI Prompt)

## Context

The CLI-streamline rule (`README.md` §3) skips `/axb-ui-plan` entirely for CLI rounds, which is
correct for a **plain, line-oriented CLI**: its contract is commands, flags, stdout/stderr and exit
codes, fully pinned by acceptance Gherkin + DSL.

A **rich terminal UI (TUI)** is a different medium: it has screens/panes, keybindings, popups, a
live status surface, focus/state, and error affordances. Acceptance Gherkin + DSL pin *behavior* but
under-specify the *interaction/UX surface* — the verification-coverage gap class this project has
been bitten by before.

`axb-ui-plan` is the skill whose *role* is exactly this (plan-side, PM-reviewable, never
`specs/truth/**`); only its *medium* (HTML) is wrong for a terminal. The framework already grew an
existing governed surface for a new medium/instance once: **PR #2** added `InterfaceKind: cli`.

## Problem

`axb-ui-plan` is HTML-hardwired: SKILL Phase 1 mandates reading `prototype-*.html`, Phase 2 writes
`ui/*.html`, and both rule files (`靜態網站雛形與實作計畫邊界判準.md`,
`高保真靜態頁面切分與Flow覆蓋判準.md`) name a *website/page*. A CLI that ships a TUI therefore has
no governed, PM-reviewable, plan-side UX artifact — the framework cannot express one, and
`axb-system-analysis` routes the whole CLI end to `/axb-dsl-refine` with no UX review step.

## Decision

Amend `axb-ui-plan` with a **terminal mode**; do **not** add an `axb-tui-plan` sibling.

1. **Medium selection by interface kind and interaction surface** (formalized as a rule, not a
   table): web/`frontend` → **HTML mode** (`ui/*.html`, unchanged); a `cli` that renders a
   **persistent, multi-region, stateful interactive surface** (screens/panes, keybindings, popups,
   live status, redrawn on input) → **terminal mode** (`ui/screens/*.txt`, **no HTML**); a
   **line-oriented** `cli` (commands/flags/stdout/stderr/exit codes) → **skipped** (contract owned
   by `/axb-dsl-refine`).
2. **Artifact shape (terminal mode):** `ui/ui-plan.md` remains the control plane with medium-adapted
   sections (`終端視覺方向`, `畫面與流程`, `Keybinding 對照表`, `狀態轉移清單`); prototype frames are
   `ui/screens/entry.txt` (launch frame) + `ui/screens/NN-<name>.txt`. The operability analogue of
   "clickable HTML" is a **keybinding table + state-transition list** so a reviewer can trace
   key → state → outcome.
3. **Domain model:** add enum `PrototypeMedium { web, terminal }` and a `Prototype.medium`
   attribute; reword `Prototype` and `UIPlan` definitions (medium-general); clarify the "CLI end
   carried by the contract owner" scenario as the line-oriented case and add the TUI contrast.
   `prototype-flows-cover-acceptance` and `prototype-plan-side-only` are unchanged.
4. **Cross-file coherence:** SKILL + rules + new terminal templates + README + role SOPs +
   `axb-system-analysis` SKILL/rules, all in the same PR as this ADR.

### Naming

- Mode name: **terminal mode** ("cli mode" collides with the plain-CLI skip case; "TUI" would need a
  glossary term).
- Medium format: separate `ui/screens/*.txt` frames (verbatim rendering, no Markdown reflow).
- **Rule filenames retained** (`靜態網站雛形…`, `高保真靜態頁面…`): the content is generalized to both
  media and each file states that the web-original scope is kept in the name; renaming is deferred as
  churn with no behavioral benefit.

## Alternatives considered

- **(b) New sibling skill `axb-tui-plan`.** Rejected: duplicates the *role* (plan-side UX artifact,
  never truth) and the whole SOP; only the medium differs. Splitting a role by medium multiplies
  drift. Amendment (c) matches the PR #2 precedent.
- **(a) Leave as-is / TUI unspecified.** Rejected: leaves the interaction surface ungoverned.
- **Medium as inline fenced ```` ```text ```` blocks inside `ui/ui-plan.md` (Q1 alt).** Rejected as
  the default: collapses the control plane and the prototype, breaking the "plan is control plane,
  prototype is a reviewable product surface" boundary; fenced blocks are still allowed for short
  illustrative snippets.
- **Reword `Prototype` only, no `medium` attribute (Q3 alt).** Rejected: the model already models the
  same "kind" idea as an enum (`InterfaceKind`); an enum + attribute is machine-checkable and
  symmetric. (Note: this is a *model* change; the JSON Schema already permits attributes/enums.)

## Consequences

- `axb-ui-plan` gains a medium branch; terminal rounds produce `ui/ui-plan.md` + `ui/screens/*.txt`
  and **must not** produce HTML. HTML rounds are unchanged.
- `axb-system-analysis` now records the CLI-TUI UX surface as **PM-produced, review-only** (mirroring
  the frontend), while the line-oriented CLI contract still hand to `/axb-dsl-refine`.
- `README.md` §3 and the PM/RD role SOPs are broadened.
- Consuming projects (tellme round 015) can produce a final `ui/ui-plan.md` + terminal frames with
  no HTML and no truth-tree writes.
- `domain-model/aixbdd.modelith.md` must be re-rendered `modelith render`.
- **Not in scope:** `axb-tasks` needs no change (it reads `ui/**` generically); `axb-spec-by-example`
  needs no change (TUI acceptance Gherkin still expresses user outcomes, not keybindings).

## Calibration / acceptance

- `modelith lint domain-model/aixbdd.modelith.yaml` → 0 errors / 0 warnings; `modelith render --check`
  → up to date.
- A terminal-mode `ui-plan.example` shows an entry frame, ≥2 screens, a keybinding table, and a flow
  (entry → intermediate → result/error).
- A reviewer can trace **key → state → outcome** from `ui/ui-plan.md` + `ui/screens/*.txt`, with no
  HTML and no `specs/truth/**` writes.
- Plain (line-oriented) CLI rounds stay **skip-by-default** — terminal mode is opt-in only when a TUI
  exists.
