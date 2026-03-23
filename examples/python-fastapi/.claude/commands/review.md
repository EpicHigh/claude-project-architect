---
description: Review code changes with severity-tagged findings and actionable fixes
allowed-tools: Bash, Read, Grep, Glob
---

# Code Review

## Process

1. Run `git diff main` to see all changes on this branch.
2. For each changed file, analyze:
   - **Correctness:** logic errors, edge cases, None handling, race conditions
   - **Security:** injection, auth bypass, secrets in code, OWASP top 10
   - **Performance:** N+1 queries, unnecessary allocations, missing indexes, blocking I/O in async
   - **Conventions:** variables: snake_case; classes: PascalCase; modules: snake_case
3. Run `pytest` — report any failures.
4. Run `ruff check .` — report any violations.

## Output Format

Tag each finding with severity:

- **blocker** — must fix before merge (bugs, security issues, data loss risk)
- **suggestion** — should fix, improves quality (performance, readability, maintainability)
- **nit** — optional, minor style preference

Format: `**file:line** — severity — description — suggested fix`

End with a summary: total findings by severity, overall assessment (approve / request changes).
