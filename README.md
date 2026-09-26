# aixbdd-tmg

This repo is a set of AI agent skills that implement a BDD (Behavior-Driven Development) workflow with clearly separated PM and RD (developer) responsibilities.

## What it is

A collection of **15+ skill definitions** (each a `SKILL.md` plus supporting `rules/` and `templates/` directories) that guide an AI through the full software development lifecycle — from requirements to working code — using Gherkin as the shared contract language. The tagline: *"PM defines acceptance criteria in Gherkin, RD turns them into automated tests, developing correct systems in one continuous flow."*

## Core idea

Most AI dev workflows blur PM/RD responsibilities (e.g., developers end up "mind-reading" unclear requirements). AIxBDD splits it:

- **PM side**: writes specs, acceptance Gherkin, and UI prototypes
- **RD side**: does technical research, API/data design, test planning, and implementation
- **Shared "truth"** (`specs/truth/**`): OpenAPI contracts, DBML data models, techstack, and interface feature/DSL files act as the single source of truth, tracked via `truth-delta.md` per plan package

## The workflow (skills in order)

1. `/axb-constitution` — set artifact governance rules
2. `/axb-specify` — create a numbered plan package with `spec.md` + checklist
3. `/axb-clarify-over-specs` — (optional) interactive requirement clarification
4. **Parallel**: PM runs `/axb-spec-by-example` (acceptance Gherkin) + `/axb-ui-plan` (HTML prototypes for a web interface; rendered terminal frames for a CLI TUI); RD runs `/axb-technical-research`
5. `/axb-system-analysis` — orchestrates `/axb-api-plan`, `/axb-data-plan` via dependency waves
6. `/axb-dsl-refine` — split acceptance criteria into executable front/back-end Gherkin + DSL
7. `/axb-tasks` — generate BDD task list (`tasks.md`)
8. `/axb-implement` — One-Shot TDD execution (red → green → refactor via `/axb-bdd`)

Supporting skills: `/axb-clarify` (user interviews), `/axb-truth-delta` (truth change tracking), `/axb-gherkin-and-dsl` (Gherkin/DSL standards + a Python topology audit script).

### Developing CLI Applications

When developing a CLI application (no web frontend or HTTP/REST API), the workflow is streamlined:

1. **Skip `/axb-ui-plan` for a plain CLI**: For a line-oriented CLI (commands, flags, arguments, exit codes, stdin/stdout/stderr) no web UI or HTML mockups are created; terminal interactions are defined directly as Gherkin acceptance scenarios in `/axb-spec-by-example`. **Exception:** a CLI that ships a rich terminal UI (TUI) runs `/axb-ui-plan` in **terminal mode** — textual screens/keybindings under `ui/screens/*.txt`, still no HTML.
2. **Lean `/axb-system-analysis`**:
   - **`/axb-api-plan`** is skipped (standalone CLIs have no OpenAPI endpoints; marked as `NOOP` in `truth-delta.md`).
   - **`/axb-data-plan`** is conditional — invoked only if the CLI manages persistent configuration (e.g. `~/.config/...`), local storage (SQLite, JSON), or complex domain state. For stateless CLI tools, it is skipped.
3. **CLI Contract via `/axb-dsl-refine`**: The CLI end is a first-class truth-tree interface — a third `InterfaceKind`, `cli`, alongside `backend`/`frontend`. The executable Gherkin feature files (`specs/truth/features/cli/**`) and their step definitions (`dsl.md`) serve as the formal CLI contract and acceptance test runner. Since no API/data/UI planner applies, `/axb-system-analysis` records no planner for it and carries the CLI end forward to its contract owner `/axb-dsl-refine`.

## Notes

- **Roles**: includes `roles/pm.yaml` and `roles/rd.yaml` role configs
- **Decisions**: changes to this repo's own governed artifacts (skills, rules, `STANDARDS.md`, the
  domain model) are recorded as short ADRs under `decisions/` — see
  [`decisions/README.md`](decisions/README.md).
- **Attribution**: this repo is a derivative of **AIxBDD** by Waterball Agent Limited, licensed under
  Apache-2.0 — see [Attribution & license](#attribution--license) and [`NOTICE`](NOTICE).

## Attribution & license

`aixbdd-tmg` is a derivative work of **AIxBDD** — *"PM defines acceptance criteria in Gherkin, RD turns
them into automated tests, developing correct systems in one continuous flow"* — the BDD workflow by
Waterball Agent Limited ([Waterball-Software-Academy/aixbdd](https://github.com/Waterball-Software-Academy/aixbdd)),
licensed under the **Apache License, Version 2.0**.

- This repository retains that license — see [`LICENSE`](LICENSE).
- The upstream attribution notice is reproduced in [`NOTICE`](NOTICE), as required by Apache-2.0 §4(d).
- Five of the inherited skills — `axb-specify`, `axb-clarify-over-specs`, `axb-tasks`, `axb-implement`,
  and `axb-technical-research` — were in turn derived by AIxBDD from
  [GitHub Spec Kit](https://github.com/github/spec-kit) (MIT); the applicable copyright and license
  notices are retained in each of those skill directories.

Changes from the upstream AIxBDD workflow: the skills are renamed with an `axb-` prefix; this repository
adds a [domain model](domain-model/), [decision records](decisions/), and PM/RD
[role configs](roles/), plus CLI-application guidance.

In short: **it's a prompt-engineering framework that turns AI-assisted development into a disciplined BDD pipeline with human review gates at each artifact.**
