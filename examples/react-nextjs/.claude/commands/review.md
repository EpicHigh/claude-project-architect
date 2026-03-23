---
description: Review code changes with severity-tagged findings and actionable fixes
allowed-tools: Bash, Read, Grep, Glob
---

# Code Review

## Process

1. Run `git diff main` to see all changes on this branch.
2. For each changed file, analyze:
   - **Correctness:** logic errors, edge cases, null/undefined handling, race conditions
   - **Security:** injection, XSS, secrets in code, auth bypass (OWASP top 10)
   - **Performance:** N+1 queries, unnecessary allocations, missing indexes, unbounded loops
   - **Conventions:** variables: camelCase; types: PascalCase; files: kebab-case
3. Run `npm test` — report any failures.
4. Run `npm run lint` — report any violations.

## Output Format

Tag each finding with severity:

- **blocker** — must fix before merge (bugs, security issues, data loss risk)
- **suggestion** — should fix, improves quality (performance, readability, maintainability)
- **nit** — optional, minor style preference

Format: `**file:line** — severity — description — suggested fix`

End with a summary: total findings by severity, overall assessment (approve / request changes).
