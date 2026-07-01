# Artifact templates & checklists

Structures and checklists for each build artifact. Adapt depth to the project — a small tool
needs a lighter version of each.

---

## Implementation plan (MILESTONES.md)

**Shape:** a sequence of milestones, worked **one at a time**, each self-contained. Build a
**thin vertical slice first** (a deployed, working skeleton that does one end-to-end thing),
then grow it — never build all layers horizontally.

**Global Definition of Done** (applies to every milestone, stated once at the top): the
project's quality gates pass — e.g. `lint`, `typecheck`, `test`, `build` all green; no
secrets committed; no forbidden dependencies.

**Each milestone contains:**
- **Objective** — one sentence: what works at the end that didn't before.
- **Scope** — the concrete work, at medium granularity (what, not every file).
- **References** — the spec sections / requirements it implements.
- **Definition of Done** — objective, runnable criteria specific to this milestone.

**Sequencing notes at the end:** dependencies between milestones, what can be parallelized,
and the instruction to give the agent **one milestone at a time** and require the full DoD
before moving on.

A typical arc: **M0 scaffold & deployed skeleton** → data foundation → core feature (the
vertical slice) → reliability/operations → hardening.

**Illustrative example — one well-formed milestone** (a neutral domain; adapt shape and depth
to your project — don't copy this verbatim):

> **M2 — Import a bank statement (CSV → parsed transactions)**
> **Objective:** a user can upload a CSV statement and see its rows parsed into typed
> transactions stored in the database.
> **Scope:** file-upload endpoint; CSV parser for the two supported bank formats; map rows to
> the `transactions` table; reject malformed rows with a clear per-row error; ignore duplicate
> imports of the same file.
> **References:** SRS §3.2 (import), §6.3 (`transactions`).
> **Definition of Done:**
> - Uploading a sample CSV inserts the expected rows; a golden-file test asserts the parsed
>   output for both formats.
> - Re-uploading the same file inserts nothing (idempotent); a test covers this.
> - A malformed row is reported with its line number and does not abort the whole import.
> - `lint`, `typecheck`, `test`, `build` are green.

Note what makes it good: the objective is one end-to-end capability; the scope is concrete but
not file-by-file; every DoD item is **objectively checkable** (a test or an observable
behavior), not "works well".

---

## Database schema

**Before writing it, close these concrete decisions with the user** (the logical spec leaves
them open):
- **Timestamps** — ISO 8601 text vs unix epoch integers? (Text is readable and sortable;
  epoch is compact.)
- **Enums** — the engine may lack a native type; emulate with TEXT + CHECK, or use integers?
- **Surrogate primary keys** — auto-increment integers vs UUIDs? (Integers are short — matters
  if the id travels in a constrained place like a callback payload.)
- **Booleans** — native, or INTEGER 0/1 + CHECK?
- **Foreign keys & cascades** — which relations get ON DELETE CASCADE? (Note engine flags,
  e.g. SQLite/libSQL needs `PRAGMA foreign_keys = ON`.)
- **Numeric types** — REAL vs INTEGER vs fixed-point for the domain values.

**Then:**
- Write the schema as the **single source of truth**, with clear comments.
- Define constraints, unique indexes (including any that enforce idempotency), and the
  indexes the access patterns need.
- **Validate**: apply it against the engine (or an equivalent) to confirm it's valid and that
  constraints/cascades behave as intended.
- Note engine caveats (no native geospatial, enum emulation, datetime-as-text, etc.).

---

## Agent instructions (AGENTS.md)

Written for a coding agent. Suggested sections:
- **Project in one paragraph** — plus pointers to the spec and README.
- **Language & style** — code language, comment language, user-facing vs internal text,
  strictness, formatting.
- **Project structure** — where each kind of code lives.
- **Validation / definition of done** — the exact commands that must pass before a task is
  "done".
- **Database rules** — schema as source of truth; how migrations are (or aren't) handled;
  where data access lives.
- **Tests** — what must be tested and at what level; the test runner.
- **Configuration & secrets** — where they're read/validated; never commit secrets.
- **Invariants — never break these** — the design decisions that are easy to violate by
  accident (numbered list). This is the highest-value section.
- **Anti-goals — do not do** — out-of-scope or forbidden actions.
- **Git conventions** — commit format, workflow.

**Eliciting the user's preferred conventions** — before generating the AGENTS.md, ask whether
the user has conventions to follow. Use these categories as *prompts* (not a mandatory form);
reuse anything already expressed or in the user's settings; propose sensible defaults if the
user has no preference, and move on. Keep it a light, cohesive ask — not a 10-question
interrogation.

1. **Language & naming** — code/comment language, user-facing text language, naming style
   (camelCase/snake_case), file/folder naming.
2. **Style & formatting** — formatter/linter, tabs vs spaces, line length, comment style,
   strictness (e.g. TypeScript `strict`, no `any`).
3. **Structure & architecture** — folder organization, preferred/avoided patterns, where data
   access lives, module separation.
4. **Error handling & logging** — exceptions vs return values, structured logs, what to log.
5. **Tests** — framework, what to test and at what level, coverage expectations, integration
   tests, mocking.
6. **Database & data** — schema/migration handling (manual vs tooling), ORM or not, where
   queries live.
7. **Dependencies** — few-dependencies preference, forbidden/preferred libraries,
   compatibility constraints (e.g. must run on edge/Workers).
8. **Git & workflow** — commit format (e.g. conventional commits), branching strategy, PRs,
   branch naming.
9. **Security & configuration** — secret handling, where config is read, what must never be
   committed.
10. **Documentation** — expected documentation level, README updates per feature, in-code
    comments.

---

## README

Reader-facing (users/installers/contributors). Suggested sections:
- **Overview** — what it is, in a few lines.
- **How it works / usage** — the user-facing behavior; commands or entry points.
- **Architecture** — components and how they interact (a small diagram helps).
- **Data sources / external services** — and how each is used.
- **Tech stack** — with a one-line rationale each.
- **Project structure** — top-level layout.
- **Setup / local development** — reproducible from scratch.
- **Deployment** — how it ships; environments.
- **Configuration** — env vars / secrets and their purpose.

---

## Scaffold

The project skeleton, usually milestone M0. Checklist:
- Project/config files (package manifest, language config, formatter/linter, build/deploy
  config).
- Folder structure matching the AGENTS.md layout (stub modules are fine).
- The minimal end-to-end path working (e.g. the app starts / responds), deployed if relevant.
- Quality gates wired and green on an empty project.

---

## Reminders
- Derive from the spec; don't invent requirements.
- Close concrete decisions with the user before emitting an artifact that forces them.
- Validate what's verifiable.
- Keep artifacts mutually consistent; re-scan after changes.
- Optimize for a coding agent: one milestone at a time, explicit invariants and anti-goals.