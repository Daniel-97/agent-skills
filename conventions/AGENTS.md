# Coding Conventions

Language-agnostic rules. Defer formatting/idioms to the project's linter and
the language's official style guide. Golden rule: optimize for the reader.

## Naming
- Names reveal intent; no abbreviations except universal ones (`id`, `url`).
- Same word for the same concept everywhere (don't mix `fetch`/`get`/`load`).
- Functions are verb phrases; variables/types are noun phrases.
- Booleans are predicates (`isValid`, `canRetry`); never negated (`isNotReady`).

## Code style & structure
- No magic numbers or strings: use a const, or an enum when appropriate.
- Minimize indentation: guard clauses and early return/continue; no Arrow
  Anti-Pattern; happy path last and unindented.
- Enums over booleans as function parameters (`render(TextMode.Compact)`,
  not `render(true)`).
- Respect layering: talk only to the layer directly below; never punch holes
  through layers.
- Separate logical blocks with empty lines.
- One responsibility per function/class/module. If describing a function needs
  "and", split it.
- KISS: prefer the most straightforward design that solves the problem.
- DRY: one authoritative source for each rule/constant/validation. DRY applies
  to knowledge, not lines: similar-looking code for different rules stays
  separate.
- Rule of Three: don't extract an abstraction before the third occurrence.
- YAGNI: build only what is needed now.
- Boy Scout Rule, but keep refactoring commits separate from behavior changes.

## Comments
- Code explains itself; comments are the exception. Prefer renaming or
  restructuring over commenting.
- Comments explain WHY (context, constraints, rejected alternatives), not how.
- A short comment stating a block's purpose is fine when faster than reading it.
- Never leave commented-out code or stale/noise comments.

## Error handling
- Fail fast: validate at the boundary, reject with a clear error.
- Never swallow errors; an empty catch is a bug. If ignoring is intentional,
  comment why.
- Handle errors at the layer with enough context to act; otherwise propagate.
  No catch-log-rethrow at every level.
- Error messages carry context: what was attempted, with which values.
- Exceptions for exceptional situations only, never for control flow.
  "Not found" / "validation failed" are results, not exceptions.

## Commit messages (Chris Beams' 7 rules)
1. Blank line between subject and body.
2. Subject ≤ 50 chars (72 hard limit).
3. Capitalize the subject line.
4. No period at the end of the subject.
5. Imperative mood: must complete "If applied, this commit will ___".
6. Wrap the body at 72 chars.
7. Body explains what and why; the code explains the how.

Also: one logical change per commit; every commit leaves the build green.
