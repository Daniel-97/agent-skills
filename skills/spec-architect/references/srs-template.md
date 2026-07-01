# SRS template & elicitation checklist

Two things live here: the **default SRS structure** to produce, and an **elicitation
checklist** of topics/questions to work through with the user. Adapt both to project size —
a small tool may collapse several subsections into a short "mini-SRS".

---

## Default SRS structure (IEEE 830 / ISO/IEC/IEEE 29148-inspired)

Use stable requirement IDs (FR-x.y for functional, NFR-x.y for non-functional).

### 1. Introduction
- **1.1 Purpose of the document** — what this SRS is for.
- **1.2 Product scope** — what the product is; explicitly what is **out of scope** (call out
  v1 exclusions).
- **1.3 Definitions, acronyms, abbreviations** — a glossary.
- **1.4 References** — external specs, APIs, standards, docs.
- **1.5 Document overview.**

### 2. Overall description
- **2.1 Product context** — how it fits with external systems/actors.
- **2.2 Main functions** — a high-level list.
- **2.3 User characteristics** — who uses it, technical level.
- **2.4 General constraints** — platform, hosting, language, regulatory, budget.
- **2.5 Assumptions and dependencies** — including facts verified against external sources.

### 3. Functional requirements
Group by feature area. Each requirement is a testable "the system shall/must…" statement
with an ID (FR-1.1, FR-1.2, …). Cover the happy path plus important edge cases, error
handling, and explicitly **accepted limitations**.

### 4. External interface requirements
- **4.1 User interfaces** — UI, commands, interaction model.
- **4.2 Software interfaces** — external services, APIs, data sources, and how each is used.
- **4.3 Communication interfaces** — protocols, webhooks, scheduling.

### 5. Non-functional requirements
Give each an ID (NFR-x.y). Typical categories:
- Performance / latency
- Reliability & delivery guarantees
- Scalability
- Security & privacy
- Maintainability
- Usability & localization
- Availability / resilience

### 6. Data model
Entities with fields, types, relationships, constraints, and indices. Keep it logical
(engine-independent) unless a concrete engine is chosen; note any storage-specific caveats
(e.g. no native geospatial support, enum emulation).

### 7. Technology stack & implementation constraints (optional)
Only when the user has chosen or asked for a stack. Language, runtime/hosting, key libraries,
and *why* each — verified against the web. Otherwise omit and keep the spec neutral.

### 8. Appendices
- Configuration parameters / secrets.
- Any protocol/encoding schemas.
- Future evolutions (deferred features, with the reason they were deferred).

---

## Elicitation checklist

Work through these incrementally — a few questions at a time, each with options and a
recommendation. Not every item applies to every project; skip what's irrelevant and note it.

### Framing
- What are you building, in one or two sentences? For whom?
- What's the single most important thing it must do well?
- Is there existing material (repo, notes, a prior version) to build from?

### Scope
- What's in scope for the first version, and what's explicitly deferred?
- What's the smallest version that would be genuinely useful?

### Functionality
- What are the main features? (Take them one cluster at a time.)
- For each feature: the normal flow, the edge cases, and what happens on error.
- Are there admin/operator functions distinct from end-user functions?

### Data
- What are the core entities? What fields does each need?
- How do they relate? What must be unique? What are the constraints?
- What's the lifecycle of the data — retention, cleanup, deletion?

### Interaction
- How do users invoke the functionality (UI, chat, commands, API, buttons)?
- Is there state between steps, or is each request self-contained?
- What are the entry points and the default/first-run experience?

### External dependencies
- What external services or data sources are needed, and for what exactly?
- For each: does it have the coverage/limits/capabilities the feature assumes? (Verify.)
- What happens when a dependency is slow or unavailable?

### Non-functional
- How fast/timely must it be? Any hard latency needs?
- What are the reliability expectations? What must never fail silently?
- Expected scale now and later? Security/privacy obligations?
- How is it monitored? How do you learn when something breaks?

### Constraints
- Platform / hosting / language constraints?
- Team, budget, deadline constraints?
- Any decisions already made that are fixed?

### Closing
- Which supporting documents do you want (README, agent instructions, schema, milestone
  plan)?
- Any accepted limitations we should record explicitly?

---

## Reminders
- Surface trade-offs and recommend; let the user decide.
- Verify load-bearing external assumptions.
- Propagate every changed decision through the whole spec and re-check consistency.
- Record accepted limitations as first-class content, not gaps.