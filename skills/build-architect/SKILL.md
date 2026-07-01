---
name: build-architect
description: >-
  Turn a consolidated software specification (ideally an SRS) into the concrete artifacts
  needed to build it: a milestone/implementation plan, a database schema, agent instructions
  (AGENTS.md), a README, and a project scaffold — all optimized for handing to a coding
  agent. Use this skill whenever the user has decided *what* to build and now wants to plan
  *how* to build it: phrasings like "turn my spec into a plan", "how should I implement
  this", "create the milestones", "generate the database schema", "set up the project",
  "give me an AGENTS.md", or otherwise moving from requirements to construction. It is the
  twin of spec-architect: spec-architect produces the "what" (the SRS); build-architect
  produces the "how". If the requirements are still vague, consolidate them first (or hand
  back to spec-architect) before generating build artifacts.
---

# Build Architect

Turn a settled specification into the concrete artifacts a team — or a coding agent — needs
to build the software: an implementation plan, a database schema, agent instructions, a
README, and a scaffold.

This is the **"how"** complement to `spec-architect`'s "what". The mode is different:
`spec-architect` is *exploratory* (it elicits requirements and opens trade-offs);
`build-architect` is *derivative and prescriptive* (the requirements are given; the job is to
translate them into artifacts faithfully). It still asks questions — but mostly about
**concrete implementation decisions the spec left open** (column types, milestone slicing,
stack details if not already chosen), not about what the product should do.

## What this skill produces (à la carte)

The user picks which artifacts they want; don't force the full set. Common ones:
- **Implementation plan** (`MILESTONES.md`) — vertical-slice milestones with verifiable
  "Definition of Done".
- **Database schema** (e.g. `schema.sql`) — concrete, validated when possible.
- **Agent instructions** (`AGENTS.md`) — invariants, anti-goals, conventions, validation
  commands.
- **README** — architecture, usage, data sources, setup, deploy.
- **Project scaffold** — config files and folder structure (often milestone M0).

If the user says "generate everything", produce a sensible default set in the recommended
order below.

## Input and prerequisites

- The ideal input is a **consolidated SRS** (ideally one produced by `spec-architect`). Read
  it fully first and ground every artifact in it.
- **Degrade gracefully.** If the user arrives with only sketchy or partial requirements, do a
  quick consolidation of the essentials first — or hand back to `spec-architect` — rather
  than generating artifacts on a vague foundation. Do not produce a schema or plan from
  requirements too thin to support them.

## Core principles

1. **Faithful, not inventive.** Derive artifacts from the spec; don't quietly introduce new
   requirements. If the spec is silent on something an artifact needs, ask — don't guess.
   **Read the SRS before asking questions**: when a spec exists but you haven't read it yet,
   read it first, so you don't ask for things it already answers. Only ask about what is
   genuinely missing or ambiguous after reading.

2. **The user chooses the artifacts.** Offer the menu; build what they ask for. Note
   dependencies (e.g. an `AGENTS.md` referencing tables is clearer once the schema exists).

3. **Recommended generation order (flexible): milestones → schema → instructions/README.**
   Start with the plan (the roadmap), then the schema (the data contract), then the
   agent/README artifacts that reference both. The order is a default, not a rule — but
   whatever the order, keep the artifacts **mutually consistent** (see principle 7).

4. **Stack: follow the spec, or elicit briefly.** If the SRS already fixes the technology
   stack, follow it. If a needed choice is missing (you can't write a schema without knowing
   the database, or a plan without knowing the runtime), elicit it briefly, user-guided. When
   you recommend a technology, **verify it against the web** — versions, APIs, and limits
   change.

5. **Close concrete details before generating.** A technical artifact forces decisions the
   logical spec left open — column types, timestamp representation, id strategy, enum
   encoding, cascade behavior. Decide these **with the user** before emitting the artifact;
   don't invent them silently. (See the schema checklist in `references/artifact-templates.md`.)

6. **Validate what's verifiable.** If an artifact can be checked, check it: run the schema
   against the engine to confirm it applies; ensure every milestone has objective, runnable
   "done" criteria; confirm the scaffold builds. A validated artifact beats a plausible one.
   When you **can't** validate (e.g. the engine isn't available), say so explicitly and
   instead review the artifact against the documented constraints — don't imply it was tested.

7. **Keep artifacts mutually consistent.** These documents cross-reference each other (the
   plan cites the schema; `AGENTS.md` cites tables and invariants; the README cites the
   architecture). When one changes, propagate to the others and re-scan for stale references
   — the same consistency discipline `spec-architect` uses.

8. **Optimize for a coding agent.** These artifacts are meant to be executed, often by a
   coding agent. So: milestones are **self-contained and delivered one at a time**, each with
   a Definition of Done the agent can run (lint/typecheck/test/build); `AGENTS.md` states
   **invariants** (rules that must never break) and **anti-goals** (things not to do); the
   schema is the single source of truth. Make the implicit explicit.

## Workflow

Adapt to the conversation; don't force a rigid script.

### 1. Ground in the requirements
Read the SRS (or consolidate sketchy input). Confirm your understanding of scope, data model,
external dependencies, and any stack already chosen. Surface gaps that would block artifact
generation.

### 2. Agree on artifacts and fill stack gaps
Confirm which artifacts the user wants. Elicit any missing implementation decisions the chosen
artifacts require (stack, DB engine, concrete types) — briefly and cohesively.

### 3. Generate in the recommended order
Milestones → schema → instructions/README (+ scaffold as needed). Before each artifact that
forces concrete choices, close those choices with the user (principle 5).

### 4. Validate
Run/check what can be checked (schema applies; milestones have objective DoD; scaffold builds).

### 5. Keep everything consistent
On any change, propagate across artifacts and re-scan for stale references.

## The artifacts

See `references/artifact-templates.md` for the structure and checklists of each. In brief:
- **Milestones:** build a thin vertical slice first (a deployed, working skeleton), then grow.
  Each milestone: objective, scope, references to the spec, and a verifiable DoD. Deliver one
  at a time.
- **Schema:** close the concrete-type decisions first; validate against the engine; make it
  the single source of truth; note engine caveats (e.g. no native geospatial, enum emulation).
- **AGENTS.md:** project overview, language/style, structure, validation commands, DB rules,
  test strategy, config/secrets, **invariants**, **anti-goals**. **Before generating it, ask
  the user whether they have code/style conventions to follow** — offer the categories in
  `references/artifact-templates.md` as prompts (language, formatting/linter, structure, error
  handling, tests, database, dependencies, git, security, docs), not as a form to fill in.
  Reuse any preferences already expressed or in the user's settings; if there are none,
  propose sensible defaults and move on. Incorporate the chosen conventions — never hard-code
  specific ones into the skill.
- **README:** what it is and how it works, architecture, data sources, tech stack, setup,
  deploy, configuration.
- **Scaffold:** the project skeleton (config, folder structure), usually the first milestone.

## Relationship with spec-architect

`build-architect` assumes the "what" is settled. If, while building artifacts, you discover
the requirements themselves are unclear or contradictory, distinguish two cases: a **small
ambiguity** — resolve it on the spot with the user and note that the SRS should be updated to
match; a **substantial gap or contradiction** — stop generating and hand back to
`spec-architect` rather than papering over it with an implementation guess.

## Reference files
- `references/artifact-templates.md` — structures and checklists for each artifact (milestone
  plan, database schema with concrete-type checklist, AGENTS.md, README, scaffold). Read it
  when generating an artifact.