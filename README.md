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
4. **Parallel**: PM runs `/axb-spec-by-example` (acceptance Gherkin) + `/axb-ui-plan` (HTML prototypes); RD runs `/axb-technical-research`
5. `/axb-system-analysis` — orchestrates `/axb-api-plan`, `/axb-data-plan` via dependency waves
6. `/axb-dsl-refine` — split acceptance criteria into executable front/back-end Gherkin + DSL
7. `/axb-tasks` — generate BDD task list (`tasks.md`)
8. `/axb-implement` — One-Shot TDD execution (red → green → refactor via `/axb-bdd`)

Supporting skills: `/axb-clarify` (user interviews), `/axb-truth-delta` (truth change tracking), `/axb-gherkin-and-dsl` (Gherkin/DSL standards + a Python topology audit script).

## Notes

- **Roles**: includes `roles/pm.yaml` and `roles/rd.yaml` role configs
- Some skills (specify, clarify-over-specs, tasks, implement, technical-research) are adapted from [GitHub Spec Kit](https://github.com/github/spec-kit) (MIT)

In short: **it's a prompt-engineering framework that turns AI-assisted development into a disciplined BDD pipeline with human review gates at each artifact.**
