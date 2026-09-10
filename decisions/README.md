# Decisions (ADRs)

Lightweight, immutable **Architecture / Decision Records** for changes to this repo's *own* governed
artifacts — the `skills/**/SKILL.md` SOPs, `skills/**/rules/*.md` judgement files,
`skills/axb-gherkin-and-dsl/STANDARDS.md`, and the canonical `domain-model/`.

This closes the "no ADR mechanism exists for the repo's own rule changes" gap surfaced by the
[tellme](https://github.com/gosharplite/tellme) grill round #4 and tracked as part of
[aixbdd-tmg#5](https://github.com/gosharplite/aixbdd-tmg/issues/5): a host-rule defect that arrives
via `/axb-clarify` → issue → PR had nowhere to be *recorded*, only fixed.

## When to write one

Write an ADR when a change:

- alters a `MUST` rule or an atomicity / ownership / single-authority judgement criterion;
- adds, removes, or repurposes a token, field, or authority that other skills or consuming projects
  depend on;
- resolves an ambiguity escalated from a consuming project (via `/axb-clarify` → issue → PR);
- introduces or retires a governance surface (a new rule file, a new truth owner, a new interface
  kind).

Typo- and editorial-only fixes do not need an ADR.

## Where they live and how they are named

- One file per decision: `decisions/NNNN-<kebab-slug>.md`, zero-padded, ascending.
- Copy `decisions/0000-adr-template.md` and keep every section.
- `Status` is one of `Proposed` / `Accepted` / `Superseded by NNNN` / `Rejected`.
- An ADR is **immutable once `Accepted`** except for its `Status` line and the index below:
  supersede it with a new ADR rather than rewriting history.
- A rule change and its ADR land in the **same PR**, so the rationale cannot drift from the rule.

## Index

| ADR | Title | Status |
| --- | --- | --- |
| [0001](0001-atomicity-fold-split-criterion.md) | Atomicity fold/split criterion (Rule 2) | Accepted |
