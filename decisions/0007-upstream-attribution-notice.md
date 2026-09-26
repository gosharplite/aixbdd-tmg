# ADR 0007 — Upstream attribution and NOTICE file

- **Status:** Accepted
- **Date:** 2026-09-26
- **Deciders:** repo owner (`@gosharplite`)
- **Supersedes:** —
- **Related:** [`NOTICE`](../NOTICE); [`README.md`](../README.md) *(Attribution & license)*;
  [`LICENSE`](../LICENSE);
  upstream [Waterball-Software-Academy/aixbdd](https://github.com/Waterball-Software-Academy/aixbdd);
  GitHub Spec Kit ([github/spec-kit](https://github.com/github/spec-kit));
  `skills/axb-{specify,clarify-over-specs,tasks,implement,technical-research}/LICENSE`.

## Context

This repository is a derivative work of **AIxBDD** — the BDD workflow published by Waterball Agent
Limited at [Waterball-Software-Academy/aixbdd](https://github.com/Waterball-Software-Academy/aixbdd)
(Apache License 2.0). The lineage is high-fidelity: 117 of the upstream's 118 files share the same path
and filename (after de-prefixing `axb-`), 50 are byte-identical, and the README tagline and the five
Spec-Kit-derived skill `LICENSE` files match upstream.

The repository carries the Apache-2.0 [`LICENSE`](../LICENSE) (the upstream license was preserved), and
its README and the five skill `LICENSE` files credit GitHub Spec Kit. However, *no* file credited the
**direct** upstream (AIxBDD / Waterball), and there was no `NOTICE` file.

## Problem

The upstream AIxBDD repository ships a `NOTICE` file:

```
AIxBDD Workflow

Copyright 2026 - 水球球特務有限公司 (Waterball Agent Limited)

Licensed under the Apache License, Version 2.0.
```

The Apache License, Version 2.0, **Section 4(d)** requires that a derivative work that is distributed
include a readable copy of the attribution notices contained within the upstream `NOTICE` file. Without
a `NOTICE`, and with only the Spec-Kit (MIT) attribution carried, this repository (i) omitted the
required attribution to its direct upstream author, and (ii) misrepresented the provenance of the
derived work — naming the Spec-Kit ancestor (one hop removed) while dropping the AIxBDD ancestor
entirely. That is a license-compliance and provenance defect.

## Decision

Resolve the gap with a minimal, additive change:

1. **Add a root `NOTICE`** that reproduces the upstream AIxBDD attribution notice **verbatim** (Apache-2.0
   §4(d)) and states the provenance chain (AIxBDD → GitHub Spec Kit) and the derived nature of this repo.
2. **Credit AIxBDD in `README.md`** — a dedicated *Attribution & license* section, plus a pointer from
   the *Notes* list.
3. **Credit AIxBDD in the five Spec-Kit-derived skill `LICENSE` files** (`axb-specify`,
   `axb-clarify-over-specs`, `axb-tasks`, `axb-implement`, `axb-technical-research`) by adding an
   upstream-provenance line above the existing Spec Kit notice; the Spec Kit MIT notice itself is
   retained unchanged.

No license change: the repository stays under Apache License 2.0, matching upstream. No skill rule,
`STANDARDS.md`, or domain-model content is altered.

## Alternatives considered

1. **Do nothing / rely on the preserved `LICENSE`.** Rejected. A general Apache text reference does not
   carry the upstream copyright holder or the Section 4(d) attribution, so it does not cure the gap.
2. **Retitle the repo as `aixbdd` and drop the `axb-` prefix.** Rejected. Out of scope, and it would churn
   every skill reference and the consuming projects for no licensing benefit.
3. **Add the attribution only to `README.md`.** Rejected. Apache §4(d) is satisfied by a `NOTICE`; a README
   mention alone is weaker and easier to drop. Both are added.
4. **Re-license the repo (e.g. to MIT).** Rejected. Keeping Apache-2.0 satisfies the upstream condition
   most simply; a re-license would itself require the upstream's consent for the derived portions.

## Consequences

- The repository now distributes the upstream `NOTICE` content and explicitly credits AIxBDD / Waterball
  Agent Limited, satisfying Apache-2.0 §4(d).
- `NOTICE` and the README *Attribution & license* section become the durable homes for provenance; a
  future change that drops the AIxBDD attribution is a governance defect, recorded here.
- No behavioral, rule, or domain-model change; consuming projects are unaffected.

## Calibration / acceptance

| Scenario | Check | Expected Outcome |
|---|---|---|
| Inspect the repo root | `NOTICE` exists and contains the upstream AIxBDD attribution verbatim | **Present** — `Copyright 2026 - 水球球特務有限公司 (Waterball Agent Limited)` is reproducible from the repo. |
| Search for the upstream author | `grep -ri waterball` over tracked files | **Match** — README, `NOTICE`, and the five skill `LICENSE` files name AIxBDD / Waterball. |
| Apache-2.0 §4(d) | A distributed derivative that includes an upstream `NOTICE` must include a readable copy of its attribution notices | **Satisfied** — the upstream `NOTICE` text is reproduced in `NOTICE`. |
| Skill provenance notices | The five Spec-Kit-derived skills' `LICENSE` files | **Retain** the Spec Kit MIT text unchanged, with the AIxBDD provenance line added. |
