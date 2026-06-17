---
name: overengineering-review
description: >
  Read-only code review for over-engineering and unnecessary complexity. Use
  when the user asks to review a project, repo, diff, feature, files, or code
  for over-design, bloat, avoidable abstractions, unnecessary dependencies,
  duplicated wrappers, reinvented standard library/native features, or "过度设计",
  "过度封装", "有没有写复杂了", "能不能简化". Supports project-wide audits,
  current-diff reviews, and targeted path reviews. Report findings only; do not
  edit code unless the user separately asks for fixes.
---

# Overengineering Review

Perform a read-only complexity review. The goal is not to criticize style; the
goal is to identify code that can be deleted, collapsed, replaced by stdlib or
native platform features, or deferred until a real second use case exists.

## Scope

- If the user names files, directories, a PR, or a diff, review only that scope.
- If the repo has uncommitted changes and the user gives no scope, review the
  current diff first.
- If no scope is obvious, do a lightweight project audit: read project
  instructions, dependency manifests, entrypoints, and the main feature modules.
- Do not scan generated/vendor/build output unless the user explicitly asks.

## Review Method

1. Understand the feature or module purpose before flagging complexity.
2. Prefer project conventions over generic advice.
3. Look for concrete removable complexity:
   - `delete`: dead code, speculative flags, unused extension points.
   - `stdlib`: hand-rolled behavior the language/runtime already provides.
   - `native`: UI/browser/database/platform feature replaces custom code.
   - `yagni`: abstraction, interface, factory, adapter, or config with one real use.
   - `dependency`: package added for a tiny or already-covered job.
   - `duplication`: wrapper or helper that only renames another helper.
   - `shrink`: same behavior in fewer clearer lines.
4. Treat safety-critical code carefully. Do not flag input validation, security,
   data-loss prevention, accessibility basics, observability needed for
   operations, or a small regression test as over-engineering.
5. If evidence is weak, put it under "possible simplifications" instead of a
   finding.

## Output

Lead with findings, ordered by expected value:

`path:line - <tag>: <what is overbuilt>. Replace with <smaller option>. Impact: <why this is safe/useful>.`

Then include:

- `Net simplification`: estimated files/lines/dependencies removable, if clear.
- `Keep as-is`: important things that looked complex but are justified.
- `Fix priority`: top 1-3 changes to make first.

If there is nothing meaningful to simplify, say:

`No clear over-engineering found in the reviewed scope.`

## Boundaries

- Report only by default; do not edit files.
- Do not block on perfect certainty. Mark assumptions clearly.
- Do not turn this into a general bug/security review. Mention serious
  correctness or security issues only under a short "out-of-scope risks" note.
