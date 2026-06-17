---
description: Review the current project, diff, or a target path for over-engineering and unnecessary complexity.
argument-hint: "[path-or-scope]"
---

# Overengineering Review

Run a read-only complexity review focused on code that can be deleted, collapsed, replaced by standard library or native platform features, or deferred until a real second use case exists.

**Requested scope (optional):** "$ARGUMENTS"

## Review Rules

1. If "$ARGUMENTS" is provided, review only that path, module, file set, or scope.
2. If no explicit scope is provided and the repo has uncommitted changes, review the current diff first.
3. If no obvious diff exists, do a lightweight project audit by reading project instructions, dependency manifests, entrypoints, and the main feature modules.
4. Do not scan generated, vendor, cache, or build output unless the user explicitly asks.

## What to Look For

Focus on concrete simplification opportunities:

- `delete`: dead code, speculative flags, unused extension points
- `stdlib`: hand-rolled behavior that the language or runtime already provides
- `native`: custom logic that should be handled by the platform, browser, database, or framework
- `yagni`: abstraction or extension point with only one real use
- `dependency`: package added for a tiny or already-covered job
- `duplication`: wrapper or helper that mainly renames another helper
- `shrink`: same behavior in fewer clearer lines

Treat validation, security checks, accessibility basics, operational observability, and small regression tests carefully. Do not label them over-engineering without strong evidence.

## Output Format

Lead with findings, ordered by expected value:

`path:line - <tag>: <what is overbuilt>. Replace with <smaller option>. Impact: <why this is safe/useful>.`

Then include:

- `Net simplification`
- `Keep as-is`
- `Fix priority`

If nothing meaningful stands out, say:

`No clear over-engineering found in the reviewed scope.`

## Usage Examples

```text
/overengineering-review:review
/overengineering-review:review src/api
/overengineering-review:review current diff
```
