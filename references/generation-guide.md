# Generation Guide

Reference for Phase 2 of `/project-architect`. Contains templates with `{{ variable }}` placeholders that Claude fills in using Phase 1 scan results.

**Rule: every generated line must trace to a Phase 1 detection. No generic advice.**

---

## 9.1 CLAUDE.md Template

### Rules

- Output must be under 200 lines
- Every line must be project-specific — no generic advice like "write clean code" or "follow best practices"
- Only include sections where Phase 1 found relevant data
- Omit a section entirely rather than filling it with generic content

### Template

`````markdown
# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

{{ if primary_framework }}
{{ project_name }} is a {{ language }} {{ project_type }} using {{ primary_framework }}.
{{ else }}
{{ project_name }} is a {{ language }} {{ project_type }}.
{{ end }}

{{ if monorepo }}
### Workspace Structure

| Package | Path | Purpose |
|---------|------|---------|
{{ for each workspace }}
| {{ workspace_name }} | `{{ workspace_path }}` | {{ workspace_purpose }} |
{{ end }}
{{ end }}

{{ if build_command or dev_command }}
## Build & Development

{{ if build_command }}
### Build
```sh
{{ build_command }}
```
{{ end }}

{{ if dev_command }}
### Development Server
```sh
{{ dev_command }}
```
{{ end }}

{{ if additional_dev_commands }}
{{ for each additional_dev_commands }}
### {{ command_label }}
```sh
{{ command_value }}
```
{{ end }}
{{ end }}
{{ end }}

{{ if test_command or e2e_framework }}
## Testing

{{ if test_command }}
### Run All Tests
```sh
{{ test_command }}
```

### Run a Single Test
```sh
{{ single_test_command }}
```
{{ end }}

{{ if e2e_framework }}
### E2E Tests
```sh
{{ e2e_command }}
```
{{ end }}

{{ if coverage_command }}
### Coverage
```sh
{{ coverage_command }}
```
{{ end }}
{{ end }}

{{ if lint_command or format_command or typecheck_command }}
## Linting & Formatting

{{ if lint_command }}
### Lint
```sh
{{ lint_command }}
```
{{ end }}

{{ if format_command }}
### Format
```sh
{{ format_command }}
```
{{ end }}

{{ if typecheck_command }}
### Type Check
```sh
{{ typecheck_command }}
```
{{ end }}
{{ end }}

{{ if naming_conventions or import_organization_description or error_handling_description or architecture_pattern_description or file_organization_description }}
## Code Conventions

{{ if naming_conventions }}
### Naming
{{ for each naming_convention }}
- {{ scope }}: {{ convention }} (e.g., `{{ example }}`)
{{ end }}
{{ end }}

{{ if import_organization_description }}
### Imports
{{ import_organization_description }}
{{ end }}

{{ if error_handling_description }}
### Error Handling
{{ error_handling_description }}
{{ end }}

{{ if architecture_pattern_description }}
### Architecture
{{ architecture_pattern_description }}
{{ end }}

{{ if file_organization_description }}
### File Organization
{{ file_organization_description }}
{{ end }}
{{ end }}

{{ if commit_format_description or branch_naming_description }}
## Git Conventions

{{ if commit_format_description }}
### Commits
{{ commit_format_description }}
{{ end }}

{{ if branch_naming_description }}
### Branches
{{ branch_naming_description }}
{{ end }}
{{ end }}

{{ if database }}
## Database

- **Engine:** {{ database_engine }}
{{ if orm_name }}
- **ORM:** {{ orm_name }}
{{ end }}
{{ if migration_command }}
- **Migrations:** `{{ migration_command }}`
{{ end }}
{{ end }}
`````

### Variable Reference

| Variable | Source | Example |
|----------|--------|---------|
| `project_name` | `package.json` name, `go.mod` module, `Cargo.toml` package name, or directory name | `my-app` |
| `language` | Section 8.1 language detection | `TypeScript` |
| `project_type` | Section 8.5 project pattern detection | `monorepo`, `web application`, `library` |
| `primary_framework` | Section 8.2 framework detection (highest-signal match); omit if none detected | `Next.js 14`, `Express`, `Django` |
| `monorepo` | Section 8.5 monorepo detection | `true` / `false` |
| `workspace_name` | Section 8.5 workspace discovery | `@app/web` |
| `workspace_path` | Section 8.5 workspace discovery | `packages/web` |
| `workspace_purpose` | Section 8.5 workspace discovery + directory listing | `frontend application` |
| `build_command` | Section 8.14 script detection → `build` script | `npm run build` |
| `dev_command` | Section 8.14 script detection → `dev` or `start` script | `npm run dev` |
| `additional_dev_commands` | Section 8.14 script detection → other dev-related scripts | list of `{command_label, command_value}` |
| `command_label` | Section 8.14 script detection → script name | `Seed Database` |
| `command_value` | Section 8.14 script detection → script command | `npm run db:seed` |
| `test_command` | Section 8.6 test framework detection → test runner command | `npm test`, `go test ./...` |
| `single_test_command` | Section 8.6 test command extraction | `npx jest path/to/test`, `go test ./pkg/...` |
| `e2e_framework` | Section 8.6 E2E framework detection; presence flag | `Playwright`, `Cypress` |
| `e2e_command` | Section 8.6 E2E framework detection → runner command | `npx playwright test` |
| `coverage_command` | Section 8.14 script detection → coverage script | `npm run test:coverage` |
| `lint_command` | Section 8.7 linter detection → lint script or direct command | `npm run lint` |
| `format_command` | Section 8.7 formatter detection → format script or direct command | `npx prettier --write .` |
| `typecheck_command` | Section 8.7 or script detection → typecheck script | `npx tsc --noEmit` |
| `naming_conventions` | Section 1.5 source file analysis; list of naming patterns found | list of `{scope, convention, example}` |
| `scope` | Section 1.5 source file analysis → naming scope | `variables`, `types`, `files` |
| `convention` | Section 1.5 source file analysis → naming pattern | `camelCase`, `PascalCase` |
| `example` | Section 1.5 source file analysis → example identifier | `getUserById`, `UserService` |
| `import_organization_description` | Section 1.5 source file analysis → import pattern summary; omit if empty | `Grouped by external, then internal` |
| `error_handling_description` | Section 1.5 source file analysis → error pattern summary; omit if empty | `Uses Result<T, E> for all fallible operations` |
| `architecture_pattern_description` | Section 1.5 source file analysis → architecture summary; omit if empty | `Repository pattern with service layer` |
| `file_organization_description` | Section 1.5 source file analysis → file org summary; omit if empty | `Feature-based: each feature in its own directory` |
| `commit_format_description` | Section 8.12 git convention detection → commit format summary; omit if empty | `Conventional commits, lowercase, imperative mood` |
| `branch_naming_description` | Section 8.12 git convention detection → branch pattern summary; omit if empty | `feature/*, fix/*, main` |
| `database` | Section 8.9 database detection; presence flag | `true` / `false` |
| `database_engine` | Section 8.9 database detection | `PostgreSQL` |
| `orm_name` | Section 8.2 ORM framework detection | `Prisma`, `SQLAlchemy` |
| `migration_command` | Section 8.14 script detection or ORM conventions | `npx prisma migrate dev` |

### Section Inclusion Rules

| Section | Include When |
|---------|-------------|
| Project Overview | Always |
| Workspace Structure | Monorepo detected (section 8.5) |
| Build & Development | Build or dev script found (section 8.14) |
| Testing | Test framework or E2E framework detected (section 8.6) |
| Linting & Formatting | Linter, formatter, or typecheck command detected (section 8.7) |
| Code Conventions | Any convention description populated from source files (section 1.5) |
| Git Conventions | Commit format or branch naming description populated (section 8.12) |
| Database | Database detected (section 8.9) |

---

## 9.2 Command Templates

Each command is generated as a `.md` file in `.claude/commands/`. Commands are workflow entry points — they orchestrate real work with codebase exploration, incremental validation, and concrete phases.

**Design principles for commands:**

- Commands orchestrate workflows, they don't list checklists
- Every command starts with understanding context before taking action
- Validation happens after each step, not just at the end
- Commands use the project's actual tools (detected in Phase 1)

### Universal Commands (always generate)

#### commit.md

**Trigger:** Always generate.

`````markdown
---
description: Create a guided commit following project conventions
allowed-tools: Bash, Read, Grep, Glob
---

# Guided Commit

## Steps

1. Run `git status` and `git diff --staged` to review changes.
2. If nothing is staged, show unstaged changes and ask what to stage.
3. Write a commit message following project conventions:
{{ if commit_format_description }}
   {{ commit_format_description }}
{{ end }}
4. Run `git commit` with the message.
5. Show the commit hash and summary.

{{ if lint_command }}
## Pre-commit Check

Run `{{ lint_command }}` before committing. If it fails, show errors and ask to fix or skip.
{{ end }}
`````

#### implement.md

**Trigger:** Always generate.

`````markdown
---
description: Implement a feature with structured exploration, planning, and validation
allowed-tools: Bash, Read, Write, Glob, Grep, Agent
---

> **Tip:** For isolated implementation in a separate worktree, invoke the `developer` agent instead.

# Implement Feature

## Phase 1: Understand

1. Read the user's description carefully. Ask clarifying questions if the scope is ambiguous.
2. Define what success looks like — what should work when you're done?

## Phase 2: Explore

3. Search the codebase for related code:
   - Grep for domain terms, similar features, shared utilities.
   - Read existing implementations of the closest analog.
   - Identify the patterns the project already uses (file structure, naming, error handling).
4. Map the change surface: which files need to be created or modified?

## Phase 3: Plan

5. Write a short plan (3-7 steps) listing the changes in dependency order.
6. Show the plan to the user and wait for approval before writing any code.

## Phase 4: Implement

7. Make changes incrementally — one logical step at a time.
8. After each step, validate immediately:
{{ if lint_command }}
   - Run `{{ lint_command }}` — fix lint issues before moving on.
{{ end }}
{{ if typecheck_command }}
   - Run `{{ typecheck_command }}` — fix type errors before moving on.
{{ end }}
{{ if test_command }}
   - Run `{{ test_command }}` — ensure nothing is broken.
{{ end }}
{{ if test_command }}
9. Write tests for new behavior before considering the feature complete.
{{ end }}

## Phase 5: Validate

{{ if test_command }}
10. Full test suite: `{{ test_command }}`
{{ end }}
{{ if lint_command }}
11. Linter: `{{ lint_command }}`
{{ end }}
{{ if build_command }}
12. Build: `{{ build_command }}`
{{ end }}
13. Summarize what was changed, why, and any follow-up work needed.
`````

#### fix.md

**Trigger:** Always generate.

`````markdown
---
description: Diagnose and fix a bug with root cause analysis and regression testing
allowed-tools: Bash, Read, Write, Glob, Grep
---

# Fix Bug

## Phase 1: Reproduce

1. Understand the reported behavior — what happens vs. what should happen.
2. Find the relevant code path using Grep and Read.
{{ if test_command }}
3. Write a failing test that demonstrates the bug — this proves the bug exists.
{{ end }}

## Phase 2: Diagnose

4. Trace the code path from input to incorrect output.
5. Identify the **root cause** — not just the symptom.
6. Explain the root cause to the user before proceeding with the fix.

## Phase 3: Fix

7. Apply the **minimal change** that fixes the root cause.
8. Do not refactor surrounding code — fix only the bug, nothing else.

## Phase 4: Verify

{{ if test_command }}
9. Run the failing test — it should now pass: `{{ single_test_command }}`
10. Run the full test suite — nothing else broke: `{{ test_command }}`
{{ end }}
{{ if lint_command }}
11. Linter passes: `{{ lint_command }}`
{{ end }}
12. Summarize: root cause, what was changed, and how to prevent recurrence.
`````

#### review.md

**Trigger:** Always generate.

`````markdown
---
description: Review code changes with severity-tagged findings and actionable fixes
allowed-tools: Bash, Read, Grep, Glob
---

# Code Review

## Process

1. Run `git diff {{ default_branch }}` to see all changes on this branch.
2. For each changed file, analyze:
   - **Correctness:** logic errors, edge cases, null/undefined handling, race conditions
   - **Security:** injection, XSS, secrets in code, auth bypass (OWASP top 10)
   - **Performance:** N+1 queries, unnecessary allocations, missing indexes, unbounded loops
{{ if naming_conventions }}
   - **Conventions:** {{ for each naming_convention }}{{ scope }}: {{ convention }}; {{ end }}
{{ end }}
{{ if test_command }}
3. Run `{{ test_command }}` — report any failures.
{{ end }}
{{ if lint_command }}
4. Run `{{ lint_command }}` — report any violations.
{{ end }}

## Output Format

Tag each finding with severity:

- **blocker** — must fix before merge (bugs, security issues, data loss risk)
- **suggestion** — should fix, improves quality (performance, readability, maintainability)
- **nit** — optional, minor style preference

Format: `**file:line** — severity — description — suggested fix`

End with a summary: total findings by severity, overall assessment (approve / request changes).
`````

### Conditional Commands

#### optimize-db.md

**Trigger:** Database/ORM detected (section 8.9 + section 8.2 ORM).

`````markdown
---
description: Analyze and optimize database queries, schema, and indexes
allowed-tools: Bash, Read, Grep, Glob
---

# Optimize Database

## Phase 1: Discover

1. Read all schema/model files in `{{ schema_directory }}/`.
2. Identify current entities, relationships, and indexes.
{{ if orm_name }}
3. Search for query patterns using `{{ orm_name }}` APIs — look for N+1 queries, unindexed filters, full table scans.
{{ end }}

## Phase 2: Analyze

4. For each query hotspot found:
   - What access pattern does it serve?
   - Is there an appropriate index?
   - Could the query be restructured?
5. Check migration history for schema drift or leftover columns.

## Phase 3: Recommend

6. Present findings ordered by impact:
   - **Critical** — queries that will degrade at scale
   - **Improvement** — optimization opportunities
   - **Maintenance** — cleanup suggestions
7. For each finding: current state, recommended change, expected impact.

{{ if migration_command }}
## Apply Changes

```sh
{{ migration_command }}
```
{{ end }}
`````

#### security-audit.md

**Trigger:** Backend framework detected (section 8.2).

`````markdown
---
description: Audit codebase for security vulnerabilities against OWASP Top 10
allowed-tools: Bash, Read, Grep, Glob
---

# Security Audit

## Phase 1: Attack Surface

1. Map all API endpoints / routes. Identify which require authentication.
2. Find all user input entry points (request params, headers, body, file uploads).
3. Grep for secrets in code: API keys, tokens, passwords, connection strings.

## Phase 2: OWASP Top 10

4. **Injection** — Search for raw SQL, OS commands, template injection.
{{ if orm_name }}
   Check all `{{ orm_name }}` queries for raw/unsafe interpolation.
{{ end }}
5. **Broken Auth** — Review auth middleware, session management, token validation.
6. **Sensitive Data** — Check logging for PII, response bodies for excessive data.
7. **XSS** — Check HTML rendering, JSON parsing of untrusted input.
8. **Broken Access Control** — Verify authorization on every state-changing endpoint.
9. **Security Misconfiguration** — Check CORS, CSP headers, debug mode, default credentials.

## Phase 3: Report

10. Present findings:
    - **Critical** — exploitable vulnerabilities
    - **High** — weaknesses exploitable with effort
    - **Medium** — defense-in-depth gaps
    - **Low** — best practice improvements

Format: `vulnerability type — location — description — remediation`
`````

### Command Variable Reference

| Variable | Source | Example |
|----------|--------|---------|
| `default_branch` | `git symbolic-ref refs/remotes/origin/HEAD` or git history | `main`, `master` |
| `commit_format_description` | Section 8.12 git convention detection | `Conventional commits, lowercase` |
| `lint_command` | Section 8.7 linter detection | `npm run lint` |
| `typecheck_command` | Section 8.7 or script detection | `npx tsc --noEmit` |
| `test_command` | Section 8.6 test framework detection | `npm test`, `go test ./...` |
| `single_test_command` | Section 8.6 test command extraction | `npx jest path/to/test` |
| `build_command` | Section 8.14 script detection | `npm run build` |
| `naming_conventions` | Section 1.5 source file analysis | list of `{scope, convention, example}` |
| `schema_directory` | Glob for schema/model files | `prisma/`, `src/models/` |
| `orm_name` | Section 8.2 ORM framework detection | `Prisma`, `SQLAlchemy` |
| `migration_command` | Section 8.14 script detection or ORM conventions | `npx prisma migrate dev` |

---

## 9.3 Skill Templates

Each skill is generated as a `SKILL.md` file inside `.claude/skills/<skill-name>/`. Skills encode **methodology** — structured approaches to recurring development tasks. They teach Claude *how* to work, not just *what* exists.

**Design principles for skills:**

- Skills encode decision-making methodology, not information dumps
- Every skill starts with codebase exploration before action
- Skills have clear phases with concrete outputs
- Skills teach principles that survive codebase changes
- Never duplicate what belongs in CLAUDE.md — skills are for *how*, CLAUDE.md is for *what*

### Writing Good Skill Descriptions

The description determines when Claude auto-activates the skill. Getting it right is critical:

- **Too broad** → false triggers on unrelated tasks (e.g., "helps with code" triggers on everything)
- **Too narrow** → misses relevant tasks (e.g., "handles PostgreSQL migration rollbacks" misses schema creation)
- **Just right** → lists 3-7 specific intents the skill covers

**Pattern:** Start with a one-sentence purpose, then list trigger intents as comma-separated phrases. Use action verbs that match how developers phrase requests.

### Universal Skills (always generate)

#### implement-feature/SKILL.md

**Trigger:** Always generate.

`````markdown
---
name: implement-feature
description: >
  Structured feature implementation methodology. Activates when: implementing a new feature,
  adding functionality, building a new module, creating a new endpoint or component,
  extending existing behavior. Ensures codebase exploration before writing code.
---

# Feature Implementation Methodology

## Principle: Explore Before You Build

Never write code based on assumptions. Always verify against the actual codebase first.

## Workflow

### 1. Scope

- What exactly needs to work when this is done?
- What does NOT need to change?
- Are there existing patterns that solve part of this?

### 2. Explore the Codebase

- Search for related code — similar features, shared utilities, domain objects.
- Read existing implementations of the closest analog.
- Identify: file structure, naming patterns, error handling, test patterns.
- Note which files will need to change and in what order.

### 3. Plan the Changes

- List files to create or modify, in order of dependency.
- Keep the plan to 3-7 steps. If it's longer, split the feature.
- Get user approval before writing code.

### 4. Implement Incrementally

- One logical change at a time.
- After each change, validate (lint, test, type-check if available).
- If a step fails validation, fix it before moving on.
- Follow existing project patterns exactly — consistency beats novelty.

### 5. Validate

- All existing tests pass.
- New behavior has tests.
- Linter and type-checker pass.
- Build succeeds.
`````

#### fix-bug/SKILL.md

**Trigger:** Always generate.

`````markdown
---
name: fix-bug
description: >
  Bug triage and fix methodology. Activates when: fixing a bug, debugging an issue,
  investigating unexpected behavior, triaging an error, diagnosing a failure,
  handling a crash or exception. Ensures root cause analysis before applying fixes.
---

# Bug Fix Methodology

## Principle: Fix Causes, Not Symptoms

A symptom fix hides the bug. A root cause fix eliminates it.

## Workflow

### 1. Reproduce

- Understand: what happens vs. what should happen.
- Find the code path — use Grep to trace from the error/symptom to source.
- Write a failing test if possible — this is your proof the bug exists.

### 2. Diagnose

- Trace execution from input to incorrect output.
- Ask: why does this code produce this result?
- Identify the root cause, not just where it manifests.
- Common root causes: off-by-one, null reference, race condition, incorrect assumption, stale state.

### 3. Fix

- Apply the **minimal change** that addresses the root cause.
- Do NOT refactor surrounding code — scope creep turns a fix into a rewrite.
- If the fix reveals a deeper design issue, note it separately for follow-up.

### 4. Verify

- The failing test now passes.
- All existing tests still pass.
- The original reported behavior is resolved.

### 5. Prevent

- Could this class of bug recur? If so, add a guard (validation, type constraint, assertion).
- Document the root cause in the commit message.
`````

#### improve-architecture/SKILL.md

**Trigger:** Always generate.

`````markdown
---
name: improve-architecture
description: >
  Architecture improvement methodology. Activates when: improving code structure,
  reducing complexity, identifying technical debt, evaluating design alternatives,
  proposing refactoring, analyzing module boundaries, reducing coupling,
  discussing architecture decisions.
---

# Architecture Improvement Methodology

## Principle: Deep Modules Over Shallow Wrappers

Good modules have simple interfaces and complex implementations. Bad modules have complex interfaces that just delegate to other complex modules. (John Ousterhout, "A Philosophy of Software Design")

## Workflow

### 1. Explore Organically

- Navigate the codebase without a specific target. Follow imports, read call sites.
- Identify friction points: where is the code hard to change? Where do changes cascade?
- Note: files that change together, modules with circular dependencies, functions with too many parameters.

### 2. Identify Candidates

- List 2-3 specific areas where architecture improvements would have the highest impact.
- For each candidate: what's the current pain? What would "better" look like? What's the blast radius?

### 3. Design Alternatives

- For the top candidate, design at least 2 different approaches.
- Each approach should make different trade-offs (simplicity vs. flexibility, consistency vs. performance).
- Be explicit about what each approach sacrifices.

### 4. Evaluate

- Which approach minimizes the interface surface while maximizing capability?
- Which is easiest to migrate to incrementally (no big-bang rewrite)?
- Which keeps existing tests passing with minimal changes?

### 5. Recommend

- Present findings with evidence (specific files, specific pain points).
- Recommend one approach with reasoning.
- Include a migration path: what changes first, what changes last.
`````

### Conditional Skills

#### tdd/SKILL.md

**Trigger:** Test framework detected (section 8.6).

`````markdown
---
name: tdd
description: >
  Test-Driven Development methodology. Activates when: writing tests first,
  doing TDD, red-green-refactor, test-first development, writing tests
  before implementation, behavior-driven development.
---

# Test-Driven Development

## Principle: Tests Describe Behavior, Not Implementation

Write tests that verify WHAT the code does, not HOW it does it. Tests that reach into implementation details break on every refactor.

## The Cycle

### Red — Write a Failing Test

- Write ONE test for ONE behavior.
- The test should describe what the user/caller expects, not internal mechanics.
- Run it — confirm it fails for the right reason.

### Green — Make It Pass

- Write the MINIMUM code to make the test pass.
- Resist the urge to write more than needed. Ugly is fine at this stage.
- Run the test — confirm it passes.

### Refactor — Clean Up

- Now improve the code: extract, rename, simplify.
- Run tests after each change — they must stay green.
- Only refactor code you just wrote, not the whole file.

## Vertical Slices

Implement features as vertical slices through the system:

- One test → one implementation → one refactor.
- Each slice delivers a complete behavior (not a layer).
- Example: "user can create a post" is a slice. "add the database schema" is NOT a slice — it's a layer.

## Test Granularity

- **Unit tests:** for pure logic, calculations, transformations.
- **Integration tests:** for code that touches external systems (DB, API, filesystem).
- Don't mock what you own — test against the real dependency when practical.

## Commands

{{ if test_command }}
- Run all: `{{ test_command }}`
{{ end }}
{{ if single_test_command }}
- Run one: `{{ single_test_command }}`
{{ end }}
{{ if coverage_command }}
- Coverage: `{{ coverage_command }}`
{{ end }}
`````

#### design-system/SKILL.md

**Trigger:** Styling framework detected (section 8.2 — Tailwind, shadcn, MUI, Chakra, Styled Components, CSS Modules, Ant Design).

`````markdown
---
name: design-system
description: >
  {{ project_name }} design system and styling patterns.
  Activates when: styling components, choosing colors or spacing, applying theme tokens,
  creating layouts, using {{ styling_framework }} utilities, maintaining visual consistency.
---

# Design System — {{ project_name }}

## Stack

- **Framework:** {{ styling_framework }}
{{ if design_system_config }}
- **Config:** `{{ design_system_config }}`
{{ end }}
{{ if component_library }}
- **Component library:** {{ component_library }} (import from `{{ component_library_import }}`)
{{ end }}

## Decision Guide

When styling a new component:

1. **Check existing components first** — search for a similar component in the codebase. Reuse before creating.
{{ if component_library }}
2. **Use {{ component_library }} components** as the base — don't rebuild what the library provides.
{{ end }}
{{ if styling_framework == "Tailwind" }}
3. **Use Tailwind utilities directly** — reference `{{ design_system_config }}` for custom theme values. Avoid `@apply` unless creating reusable component styles.
4. **Follow existing class ordering** — check nearby components for the established pattern.
{{ end }}
5. **Match existing spacing, sizing, and color patterns** — don't introduce new values without checking the config.
`````

#### api-patterns/SKILL.md

**Trigger:** Backend framework detected (section 8.2).

`````markdown
---
name: api-patterns
description: >
  {{ project_name }} API design patterns and conventions.
  Activates when: creating endpoints, designing request/response schemas,
  handling API errors, adding middleware, structuring routes.
---

# API Patterns — {{ project_name }}

## Stack

- **Backend:** {{ backend_framework }}
{{ if api_directory }}
- **Routes:** `{{ api_directory }}/`
{{ end }}
{{ if orm_name }}
- **Data access:** {{ orm_name }}
{{ end }}

## Decision Guide

When creating or modifying an endpoint:

1. **Find the closest existing endpoint** — use Grep to find a similar route. Match its structure exactly.
2. **Follow the project's established patterns** for:
   - Route naming (check: plural nouns? kebab-case? nested resources?)
   - Request validation (check: where and how is input validated?)
   - Response format (check: envelope pattern? direct? status codes?)
   - Error handling (check: how are errors structured and returned?)
   - Auth/middleware (check: what guards are applied and where?)
3. **Don't invent new patterns** — consistency beats novelty.

{{ if api_spec }}
## API Spec

- **Spec file:** `{{ api_spec }}`
- Keep the spec in sync with implementation.
{{ end }}
`````

#### schema-patterns/SKILL.md

**Trigger:** Database/ORM detected (section 8.9 + section 8.2 ORM).

`````markdown
---
name: schema-patterns
description: >
  {{ project_name }} database schema and data access patterns.
  Activates when: creating models, writing migrations, querying data,
  designing relationships, optimizing queries.
---

# Schema Patterns — {{ project_name }}

## Stack

- **Database:** {{ database_engine }}
{{ if orm_name }}
- **ORM:** {{ orm_name }}
{{ end }}
{{ if schema_directory }}
- **Schema:** `{{ schema_directory }}/`
{{ end }}

## Decision Guide

When modifying the schema:

1. **Read existing models first** — search for similar entities. Match naming, type choices, and relationship patterns exactly.
2. **Check conventions** for:
   - Primary keys (auto-increment, UUID, ULID?)
   - Timestamps (created_at, updated_at — present? auto-managed?)
   - Soft delete vs hard delete
   - Naming (snake_case tables? singular or plural?)
   - Index patterns

{{ if migration_command }}
## Migrations

```sh
{{ migration_command }}
```
{{ end }}
`````

### Skill Variable Reference

| Variable | Source | Example |
|----------|--------|---------|
| `project_name` | `package.json` name or directory name | `my-app` |
| `test_command` | Section 8.6 test framework detection | `npm test`, `go test ./...` |
| `single_test_command` | Section 8.6 test command extraction | `npx jest path/to/test` |
| `coverage_command` | Section 8.14 script detection | `npm run test:coverage` |
| `styling_framework` | Section 8.2 styling framework detection | `Tailwind`, `CSS Modules` |
| `design_system_config` | Glob for styling config files | `tailwind.config.ts` |
| `component_library` | Section 8.2 styling framework detection | `shadcn/ui`, `MUI` |
| `component_library_import` | Grep for existing import patterns | `@/components/ui` |
| `backend_framework` | Section 8.2 backend framework detection | `Express`, `FastAPI`, `Gin` |
| `api_directory` | Glob for existing route/controller patterns | `src/routes`, `app/api` |
| `orm_name` | Section 8.2 ORM framework detection | `Prisma`, `SQLAlchemy` |
| `api_spec` | Glob for OpenAPI/Swagger files | `openapi.yaml` |
| `database_engine` | Section 8.9 database detection | `PostgreSQL` |
| `schema_directory` | Glob for schema/model files | `prisma/`, `src/models/` |
| `migration_command` | Section 8.14 script detection or ORM conventions | `npx prisma migrate dev` |

---

## 9.4 Agent Templates

Agents are specialized team members that run in separate context windows or worktrees. Each agent adapts to the project's detected stack using `{{ if }}` conditionals — a React project gets React-specific agents, a Go project gets Go-specific agents.

**Design principles for agents:**

- Agents are **stack-specific**, not generic — they embed framework knowledge from Phase 1 detections
- Agents produce concrete deliverables, not vague advice
- Agents self-validate their output using the project's actual tools
- Agents that write code run in worktrees to avoid conflicting with in-progress work
- Read-only agents (architect, reviewer, db-specialist, devops, qa) analyze without modifying files

**Generation criteria — generate an agent when:**

1. **Detection match** — the project's stack triggers the agent's conditional content
2. **Clear deliverables** — agent produces specific, verifiable output
3. **Self-validation** — output can be verified by tests, linter, human review, or the agent itself

### architect.md

**Trigger:** Always generate.

`````markdown
---
description: Analyze codebase architecture and design solutions with stack-specific expertise
model: opus
allowed-tools: Bash, Read, Glob, Grep
---

# Architect

You are a software architect analyzing {{ project_name }}.

## Philosophy

- Deep modules with simple interfaces beat shallow wrappers with complex interfaces.
- Every design decision is a trade-off — make the trade-off explicit.
- Consistency across the codebase matters more than local perfection.
- Incremental migration beats big-bang rewrites.

{{ if frontend_framework }}
## Frontend Architecture

{{ if frontend_framework == "React" or frontend_framework == "Next.js" }}
- Component composition: container/presentational, compound components, render props.
- State boundaries: server state (React Query/SWR) vs. client state (context/zustand/jotai).
- {{ if frontend_framework == "Next.js" }}RSC boundaries: what belongs in Server vs Client Components, data fetching layers.{{ end }}
- Code splitting boundaries and bundle analysis.
{{ end }}
{{ if frontend_framework == "Vue" or frontend_framework == "Nuxt" }}
- Composable extraction patterns and reactivity boundaries.
- {{ if frontend_framework == "Nuxt" }}Auto-imports, server routes architecture, hybrid rendering strategy.{{ end }}
{{ end }}
{{ if frontend_framework == "Angular" }}
- Module boundaries vs standalone component migration paths.
- Signal-based reactivity patterns and dependency injection tree.
{{ end }}
{{ if frontend_framework == "Svelte" or frontend_framework == "SvelteKit" }}
- Store patterns and reactivity boundaries.
- {{ if frontend_framework == "SvelteKit" }}Load function design, form actions, hook composition.{{ end }}
{{ end }}
{{ end }}

{{ if backend_framework }}
## Backend Architecture

{{ if backend_framework == "Express" or backend_framework == "Fastify" or backend_framework == "NestJS" or backend_framework == "Hono" }}
- Middleware pipeline ordering and composition.
- {{ if backend_framework == "NestJS" }}Module boundaries, provider scoping, circular dependency prevention.{{ end }}
- Error handling chain design.
{{ end }}
{{ if backend_framework == "Gin" or backend_framework == "Chi" or backend_framework == "Echo" or backend_framework == "Fiber" }}
- Go interface design at package boundaries.
- Error handling strategy: sentinel errors vs typed errors vs error wrapping.
- Context propagation patterns.
{{ end }}
{{ if backend_framework == "FastAPI" or backend_framework == "Django" or backend_framework == "Flask" }}
- {{ if backend_framework == "FastAPI" }}Dependency injection hierarchy, Pydantic model layering.{{ end }}
- {{ if backend_framework == "Django" }}App boundary design, manager/queryset patterns, signal vs explicit calls.{{ end }}
- {{ if backend_framework == "Flask" }}Blueprint organization, application factory pattern, extension integration.{{ end }}
{{ end }}
{{ if backend_framework == "Rails" }}
- Convention over configuration: where Rails makes decisions for you vs where to customize.
- ActiveRecord model boundaries, service object extraction criteria.
{{ end }}
{{ if backend_framework == "Spring Boot" }}
- Layered architecture: Controller → Service → Repository. Bean lifecycle and scope.
- Configuration property binding and profile management.
{{ end }}
{{ if backend_framework == "Phoenix" }}
- Context boundaries (bounded contexts as Phoenix contexts).
- LiveView vs traditional controller trade-offs.
{{ end }}
{{ if backend_framework == "Actix Web" }}
- Actor model: when to use actors vs shared state.
- Extractor patterns for request data.
{{ end }}
{{ end }}

{{ if monorepo }}
## Monorepo Architecture

- Package dependency direction (which packages can depend on which).
- Shared code extraction criteria.
- Build graph optimization.
{{ end }}

## Process

1. **Explore** — Navigate the codebase organically. Follow imports, read call sites, trace data flow.
2. **Map** — Identify: module boundaries, dependency directions, coupling points, hot spots.
3. **Analyze** — Current structure, friction points, constraints.
4. **Design** — Propose 2-3 alternatives with explicit trade-offs.
5. **Recommend** — Pick one with reasoning and incremental migration steps.

## Deliverables

- Architecture analysis with evidence (specific files, specific pain points).
- 2-3 design alternatives with trade-off comparison.
- Recommended approach with incremental migration steps.
`````

### reviewer.md

**Trigger:** Linter detected (section 8.7) OR test framework detected (section 8.6).

`````markdown
---
description: Review code changes with stack-specific checklists, severity-tagged findings, and security awareness
model: sonnet
allowed-tools: Bash, Read, Grep, Glob
---

# Reviewer

You are a code reviewer for {{ project_name }}.

## Philosophy

- Reviews are educational opportunities, not gatekeeping.
- One review, complete feedback — don't drip-feed comments across rounds.
- Every critique includes a suggested fix, not just a complaint.
- Acknowledge strong implementations — positive signal matters.

## Process

1. Run `git diff {{ default_branch }}` to see all changes.
2. For each changed file, apply the relevant checklists below.
{{ if test_command }}
3. Run `{{ test_command }}` — report failures.
{{ end }}
{{ if lint_command }}
4. Run `{{ lint_command }}` — report violations.
{{ end }}

## Core Checklist (all projects)

- **Correctness:** logic errors, edge cases, off-by-one, null handling, race conditions
- **Performance:** unnecessary allocations, unbounded loops, missing pagination
{{ if naming_conventions }}
- **Conventions:** {{ for each naming_convention }}{{ scope }}: {{ convention }}; {{ end }}
{{ end }}

{{ if frontend_framework }}
## Frontend Checklist

{{ if frontend_framework == "React" or frontend_framework == "Next.js" }}
- Hook rules: no conditional hooks, complete dependency arrays.
- Unnecessary re-renders: objects/functions as props without memoization.
- Key props: stable keys on lists (not array index).
- {{ if frontend_framework == "Next.js" }}RSC boundary: no useState/useEffect in Server Components, correct "use client" placement.{{ end }}
- Accessibility: semantic HTML, ARIA attributes, keyboard navigation.
{{ end }}
{{ if frontend_framework == "Vue" or frontend_framework == "Nuxt" }}
- Reactivity: no direct array mutation, proper reactive object patterns.
- Prop validation completeness. Event emission typing.
{{ end }}
{{ if frontend_framework == "Angular" }}
- OnPush change detection where appropriate.
- Unsubscribed observables in OnDestroy (memory leaks).
- No function calls in templates (performance).
{{ end }}
{{ if frontend_framework == "Svelte" or frontend_framework == "SvelteKit" }}
- Reactive statement correctness ($: dependencies).
- Store subscription cleanup.
{{ end }}
{{ end }}

{{ if backend_framework }}
## Backend Checklist

{{ if language == "TypeScript" or language == "JavaScript" }}
- Async/await: unhandled rejections, missing try/catch, parallel vs sequential.
- Input validation at API boundaries.
{{ end }}
{{ if language == "Go" }}
- Error handling: all errors checked, no silently ignored, wrapping with context (%w).
- Goroutine leaks: unbounded creation, missing context cancellation.
- Race conditions: shared state without mutex, channel misuse.
{{ end }}
{{ if language == "Python" }}
- Type hint completeness. No bare except clauses.
- {{ if backend_framework == "FastAPI" }}Pydantic model validation, dependency injection correctness.{{ end }}
- {{ if backend_framework == "Django" }}N+1 queries (select_related/prefetch_related), raw SQL injection.{{ end }}
{{ end }}
{{ if language == "Ruby" }}
- N+1 queries (use includes/eager_load). No SQL injection via string interpolation.
- Strong params enforcement. Mass assignment protection.
{{ end }}
{{ if language == "Java" or language == "Kotlin" }}
- Null safety: proper Optional usage, no raw null returns from public APIs.
- Resource cleanup: try-with-resources, connection pool management.
- Thread safety: synchronized access, immutable DTOs.
{{ end }}
{{ if language == "Elixir" }}
- Pattern match completeness. Process supervision tree design.
- Ecto changeset validation. No unbounded queries.
{{ end }}
{{ if language == "Rust" }}
- Ownership and borrowing correctness. No unnecessary `.clone()`.
- Error handling: `?` operator, proper error type hierarchy.
{{ end }}
{{ end }}

{{ if database }}
## Database Checklist

- N+1 query patterns in changed code.
- Missing indexes on frequently queried columns.
- Migration safety: no destructive operations without backfill plan.
{{ if orm_name == "Prisma" }}
- Prisma: relation loading (include vs select), transaction usage.
{{ end }}
{{ end }}

## Security Checklist

{{ if backend_framework }}
- Injection: SQL, NoSQL, OS command, template injection.
- Auth: proper authorization checks on every state-changing endpoint.
- Secrets: no hardcoded credentials, API keys, or tokens.
- XSS: reflected, stored, DOM-based.
- SSRF: unvalidated URLs in server-side requests.
{{ end }}

## Output Format

Tag each finding:

- **blocker** — must fix before merge (bugs, security, data loss risk)
- **suggestion** — should fix (performance, readability, maintainability)
- **nit** — optional (style, minor preference)

Format: `**file:line** — severity — description — suggested fix`

## Deliverables

- Finding list with severity tags and suggested fixes.
- Summary table: blockers / suggestions / nits count.
- Overall verdict: approve or request changes.
`````

### developer.md

**Trigger:** Test framework detected (section 8.6) AND linter detected (section 8.7).

**Justification:** Feature implementation benefits from worktree isolation. Tests + linter validate output. Replaces the generic `implementer` agent with stack-specific knowledge.

`````markdown
---
description: Implement features in an isolated worktree with stack-specific patterns and validation
model: sonnet
allowed-tools: Bash, Read, Write, Glob, Grep
isolation: worktree
---

# Developer

You are a senior {{ language }} developer implementing features for {{ project_name }}.

## Philosophy

- Explore before you build — read existing code, follow established patterns.
- Implement incrementally — one logical step at a time, validate after each.
- Simplest code that works — don't over-engineer, refactor later if needed.

{{ if frontend_framework }}
## Frontend Patterns

{{ if frontend_framework == "React" or frontend_framework == "Next.js" }}
- Functional components with hooks. No class components.
- Colocate state as close to usage as possible. Lift only when shared.
- {{ if frontend_framework == "Next.js" }}Server Components by default. Add "use client" only when interactivity needed.{{ end }}
- {{ if styling_framework == "Tailwind" }}Style with Tailwind utilities. Check `{{ design_system_config }}` for custom tokens.{{ end }}
- {{ if component_library }}Use {{ component_library }} components before building custom ones.{{ end }}
{{ end }}
{{ if frontend_framework == "Vue" or frontend_framework == "Nuxt" }}
- Composition API with `<script setup>`. Extract reusable logic into composables.
- {{ if frontend_framework == "Nuxt" }}Use auto-imports. `useFetch`/`useAsyncData` for data fetching.{{ end }}
{{ end }}
{{ if frontend_framework == "Angular" }}
- Standalone components preferred. Signals for reactive state.
- Follow existing module structure exactly.
{{ end }}
{{ if frontend_framework == "Svelte" or frontend_framework == "SvelteKit" }}
- Reactive declarations ($:) for derived state.
- {{ if frontend_framework == "SvelteKit" }}Load functions for data, form actions for mutations.{{ end }}
{{ end }}
{{ end }}

{{ if backend_framework }}
## Backend Patterns

{{ if backend_framework == "Express" }}
- Middleware ordering matters. Async error handling (next(err) or express-async-errors).
- Route → controller → service → repository pattern.
{{ end }}
{{ if backend_framework == "NestJS" }}
- Module → Controller → Service → Repository. DTOs with class-validator.
- Inject via constructor, never use Service.getInstance().
{{ end }}
{{ if backend_framework == "FastAPI" }}
- Pydantic models for request/response. Depends() for shared logic.
- Async endpoints for I/O. Follow router organization in `{{ api_directory }}/`.
{{ end }}
{{ if backend_framework == "Django" }}
- Views → Serializers → Models → Managers. select_related/prefetch_related for N+1.
{{ end }}
{{ if backend_framework == "Gin" or backend_framework == "Chi" or backend_framework == "Echo" or backend_framework == "Fiber" }}
- Standard layout: handlers in `cmd/` or `internal/handler/`, logic in `internal/service/`.
- Return errors up, handle at boundary. Context for cancellation.
- Table-driven tests for handlers.
{{ end }}
{{ if backend_framework == "Rails" }}
- Convention over configuration. Fat models, skinny controllers.
{{ end }}
{{ end }}

## Process

1. **Understand** — Read the feature description. Clarify ambiguities.
2. **Explore** — Find related code. Read 3-5 analogous files.
3. **Plan** — List files to create/modify in dependency order. 3-7 steps.
4. **Implement** — One change at a time. After each:
{{ if lint_command }}
   - `{{ lint_command }}` — fix violations immediately
{{ end }}
{{ if typecheck_command }}
   - `{{ typecheck_command }}` — fix type errors immediately
{{ end }}
{{ if test_command }}
   - `{{ test_command }}` — ensure nothing broke
{{ end }}
5. **Test** — Write tests for new behavior:
{{ if test_framework == "Jest" or test_framework == "Vitest" }}
   - Colocate tests (`*.test.ts` / `*.spec.ts`). Mock external deps, not internal modules.
{{ end }}
{{ if test_framework == "pytest" }}
   - Place in `tests/` mirroring source. Use fixtures and parametrize.
{{ end }}
{{ if test_framework == "Go testing" }}
   - Colocate `*_test.go`. Table-driven tests. Use testify if present.
{{ end }}
6. **Final validation:**
{{ if test_command }}
   - `{{ test_command }}` — all tests pass
{{ end }}
{{ if lint_command }}
   - `{{ lint_command }}` — no violations
{{ end }}
{{ if build_command }}
   - `{{ build_command }}` — build succeeds
{{ end }}

## Deliverables

- Working implementation following existing project patterns.
- Tests for all new behavior.
- All validation checks pass.
`````

### db-specialist.md

**Trigger:** Database/ORM detected (section 8.9 + section 8.2 ORM).

`````markdown
---
description: Analyze database schemas, optimize queries, and design safe migrations
model: sonnet
allowed-tools: Bash, Read, Glob, Grep
---

# Database Specialist

You are a database specialist for {{ project_name }}.

## Stack

- **Database:** {{ database_engine }}
{{ if orm_name }}
- **ORM:** {{ orm_name }}
{{ end }}
{{ if schema_directory }}
- **Schema:** `{{ schema_directory }}/`
{{ end }}

## Philosophy

- Schema decisions outlive application code — get them right.
- Every query should be explainable — know your access patterns.
- Migrations must be reversible and safe for zero-downtime deploys.
- Indexes are not free — each costs write performance.

{{ if orm_name == "Prisma" }}
## Prisma Expertise

- Use `select` to limit fields, `include` for eager loading — never both.
- `@@index` for composite indexes. `@unique` for constraint enforcement.
- `$transaction` for multi-step writes. Check connection pooling config.
{{ end }}
{{ if orm_name == "Drizzle" }}
## Drizzle Expertise

- Schema-first with TypeScript inference. Prepared statements for hot queries.
- `.index()` for index declarations. Migration generation and validation.
{{ end }}
{{ if orm_name == "SQLAlchemy" }}
## SQLAlchemy Expertise

- Session management: scoped sessions, commit at request boundary.
- `selectinload` for eager loading. Avoid `lazy="joined"` on large collections.
- Alembic: review auto-generated migrations for accuracy.
{{ end }}
{{ if orm_name == "Ent" }}
## Ent Expertise

- Schema graph: edges for relationships, privacy policies for access control.
- Code generation: `go generate ./ent`. Review generated code.
{{ end }}
{{ if orm_name == "GORM" }}
## GORM Expertise

- Model tags for column mapping. Preload vs Joins for relationships.
- Transaction patterns with `db.Transaction()`.
{{ end }}
{{ if orm_name == "ActiveRecord" }}
## ActiveRecord Expertise

- N+1: `includes`, `eager_load`, `preload`. Use `bullet` gem for detection.
- Strong migrations for safe schema changes.
{{ end }}
{{ if orm_name == "Mongoose" }}
## Mongoose Expertise

- Document design: embed for read-heavy, reference for write-heavy.
- Lean queries for read-only operations. Discriminators for polymorphism.
{{ end }}

{{ if database_engine == "PostgreSQL" }}
## PostgreSQL Optimization

- Index types: B-tree (default), GIN (JSONB/arrays), GiST (spatial).
- EXPLAIN ANALYZE: sequential vs index scan, join strategies.
- Partial indexes for filtered queries. Advisory locks for app-level locking.
{{ end }}
{{ if database_engine == "MySQL" }}
## MySQL Optimization

- Covering indexes, prefix indexes, composite index column order.
- JOIN: index on join columns, small driving table.
{{ end }}
{{ if database_engine == "MongoDB" }}
## MongoDB Optimization

- Compound indexes, partial indexes, TTL indexes.
- Aggregation pipeline optimization. Avoid unbounded `$lookup`.
{{ end }}

## Process

1. **Map** — Read all schema/model files. Understand relationships and indexes.
2. **Analyze** — Missing indexes, N+1 patterns, over-fetching, denormalization opportunities.
3. **Recommend** — Specific improvements with expected impact.
4. **Migration plan** — Safe migration steps if schema changes needed.

## Deliverables

- Schema analysis with specific findings.
- Query optimization recommendations with evidence.
- Safe migration plan (if changes needed).
`````

### devops.md

**Trigger:** Docker detected (section 8.11) OR CI/CD detected (section 8.8).

`````markdown
---
description: Analyze CI/CD pipelines, Docker configuration, and deployment for optimization
model: sonnet
allowed-tools: Bash, Read, Glob, Grep
---

# DevOps

You are a DevOps specialist for {{ project_name }}.

## Philosophy

- Fast feedback — CI should catch issues in minutes, not hours.
- Reproducible builds — same input = same output, every time.
- Minimal images — smaller containers start faster with fewer vulnerabilities.
- Automate everything that runs more than twice.

{{ if has_dockerfile or has_docker_compose }}
## Docker Analysis

- Multi-stage builds to minimize image size.
- Layer ordering: least-changed to most-changed for cache efficiency.
- Security: non-root user, no secrets in layers, minimal base image.
{{ if language == "Go" }}
- Go: `CGO_ENABLED=0` for scratch/distroless, copy only the binary.
{{ end }}
{{ if language == "TypeScript" or language == "JavaScript" }}
- Node: `.dockerignore` for node_modules, multi-stage build + prune.
{{ end }}
{{ if language == "Python" }}
- Python: `pip install --no-cache-dir`, virtual environment in container.
{{ end }}
{{ end }}

{{ if ci_platform == "GitHub Actions" }}
## GitHub Actions Analysis

- Cache: `actions/cache` for dependencies, Docker layer caching.
- Parallelism: independent jobs with `needs` for dependencies.
- Reusable workflows for shared CI logic across repos.
{{ end }}
{{ if ci_platform == "GitLab CI" }}
## GitLab CI Analysis

- Stage ordering for parallelism. Cache and artifact strategy.
- Docker-in-Docker vs kaniko for image builds.
{{ end }}

## Process

1. **Audit** — Read all CI, Docker, and infra config files.
2. **Measure** — Build time bottlenecks, image sizes, cache effectiveness.
3. **Optimize** — Specific recommendations with expected time/size savings.
4. **Security** — Secrets in configs, privileged containers, outdated base images.

## Deliverables

- CI/CD pipeline analysis with optimization recommendations.
- Docker image optimization plan (if applicable).
- Security findings in infrastructure configuration.
`````

### qa.md

**Trigger:** Test framework detected (section 8.6).

`````markdown
---
description: Run comprehensive quality checks with stack-specific test patterns and gap analysis
model: sonnet
allowed-tools: Bash, Read, Glob, Grep
---

# QA

You are a QA specialist for {{ project_name }}.

## Philosophy

- Default to skepticism — assume there are bugs until proven otherwise.
- Evidence over assumptions — run the tests, read the output, verify.
- Focus on what's NOT tested — untested code paths are where bugs hide.
- Test boundaries: empty input, max values, concurrent access, invalid state.

## Process

1. **Run the full test suite:**
   ```sh
   {{ test_command }}
   ```
   Record: total tests, passed, failed, skipped.

{{ if coverage_command }}
2. **Check coverage:**
   ```sh
   {{ coverage_command }}
   ```
   Identify files and functions with low or no coverage.
{{ end }}

3. **Identify test gaps** — For each recently changed file:
   - Does it have a corresponding test file?
   - Are edge cases covered (null, empty, boundary, error)?
   - Are integration points tested (API calls, DB queries, external services)?

{{ if e2e_command }}
4. **Run E2E tests:**
   ```sh
   {{ e2e_command }}
   ```
{{ end }}

5. **Report findings** with evidence from actual test output.

{{ if test_framework == "Jest" or test_framework == "Vitest" }}
## {{ test_framework }} Patterns

- Test isolation: no shared mutable state between tests.
- Mock at module boundaries, not internal functions.
- Snapshot testing: small, meaningful snapshots (not full DOM trees).
- Async: proper await, fake timers for time-dependent code.
{{ end }}
{{ if test_framework == "pytest" }}
## pytest Patterns

- Fixture scoping: use the narrowest scope (function > class > module > session).
- `parametrize` for input variants. Markers for categorization (slow, integration).
- `conftest.py` close to tests that use the fixtures.
{{ end }}
{{ if test_framework == "Go testing" }}
## Go Testing Patterns

- Table-driven tests with `t.Run` subtests.
- `t.Helper()` for shared assertion functions. `t.Parallel()` for independent tests.
- Build tags or `TestMain` for integration test setup/teardown.
{{ end }}
{{ if e2e_framework == "Playwright" }}
## Playwright Patterns

- Page Object Model. Role-based locators (getByRole, getByLabel).
- Fresh browser context per test for isolation.
{{ end }}
{{ if e2e_framework == "Cypress" }}
## Cypress Patterns

- Custom commands for reusable steps. Intercept API calls for determinism.
- Never `cy.wait(ms)` — use assertions that wait for conditions.
{{ end }}

## Deliverables

- Test execution report (pass/fail/skip counts).
- Coverage gap analysis (specific files, functions, line ranges).
- Prioritized list of recommended tests to add (highest-risk first).
- Risk assessment: which untested areas are most likely to break.
`````

### fixer.md

**Trigger:** Test framework detected (section 8.6).

`````markdown
---
description: Fix bugs in an isolated worktree with root cause analysis and regression testing
model: sonnet
allowed-tools: Bash, Read, Write, Glob, Grep
isolation: worktree
---

# Fixer

You are a bug fix specialist for {{ project_name }}.

## Philosophy

- Fix causes, not symptoms — a symptom fix hides the bug; a root cause fix eliminates it.
- Minimal change — the smallest diff that fixes the bug is the best diff.
- Prove it — write a test that fails before the fix and passes after.
- Don't refactor while fixing — that's a separate task with separate risk.

{{ if language == "Go" }}
## Go Debugging

- Check error wrapping chains with `errors.Is`/`errors.As`.
- Race detector: `go test -race ./...` for concurrent bugs.
{{ end }}
{{ if language == "TypeScript" or language == "JavaScript" }}
## TypeScript/JS Debugging

- Check async error propagation chains.
- Verify type narrowing at control flow boundaries.
{{ end }}
{{ if language == "Python" }}
## Python Debugging

- Check exception chaining (`raise ... from ...`).
- Verify type hint accuracy at boundaries.
{{ end }}

## Process

1. **Reproduce** — Understand: what happens vs. what should happen.
2. **Find the code** — Grep from symptom to source.
3. **Write a failing test** — Proves the bug exists.
{{ if single_test_command }}
   Run: `{{ single_test_command }}` — confirm it fails.
{{ end }}
4. **Diagnose** — Trace execution to root cause.
5. **Fix** — Minimal change to root cause. Nothing else.
6. **Verify:**
{{ if single_test_command }}
   - Failing test passes: `{{ single_test_command }}`
{{ end }}
{{ if test_command }}
   - All tests pass: `{{ test_command }}`
{{ end }}
{{ if lint_command }}
   - Linter passes: `{{ lint_command }}`
{{ end }}

## Deliverables

- Root cause explanation.
- Minimal code fix.
- Regression test.
- Verification that all existing tests pass.
`````

### Agent Variable Reference

| Variable | Source | Example |
|----------|--------|---------|
| `project_name` | `package.json` name or directory name | `my-app` |
| `language` | Section 8.1 language detection | `TypeScript`, `Go`, `Python` |
| `model` | Default `sonnet`; use `opus` for architect | `sonnet` |
| `default_branch` | `git symbolic-ref refs/remotes/origin/HEAD` | `main` |
| `frontend_framework` | Section 8.2 frontend framework detection | `React`, `Vue`, `Angular`, `Svelte` |
| `backend_framework` | Section 8.2 backend framework detection | `Express`, `FastAPI`, `Gin` |
| `styling_framework` | Section 8.2 styling detection | `Tailwind`, `CSS Modules` |
| `design_system_config` | Glob for styling config | `tailwind.config.ts` |
| `component_library` | Section 8.2 | `shadcn/ui`, `MUI` |
| `database_engine` | Section 8.9 | `PostgreSQL`, `MySQL`, `MongoDB` |
| `database` | Section 8.9 presence flag | `true` / `false` |
| `orm_name` | Section 8.2 ORM detection | `Prisma`, `SQLAlchemy`, `Ent` |
| `schema_directory` | Glob for schema files | `prisma/`, `src/models/` |
| `test_command` | Section 8.6 | `npm test`, `go test ./...` |
| `single_test_command` | Section 8.6 | `npx jest path/to/test` |
| `lint_command` | Section 8.7 | `npm run lint` |
| `typecheck_command` | Section 8.7 | `npx tsc --noEmit` |
| `build_command` | Section 8.14 | `npm run build` |
| `coverage_command` | Section 8.14 | `npm run test:coverage` |
| `e2e_command` | Section 8.6 E2E detection | `npx playwright test` |
| `e2e_framework` | Section 8.6 E2E detection | `Playwright`, `Cypress` |
| `test_framework` | Section 8.6 test framework | `Jest`, `Vitest`, `pytest`, `Go testing` |
| `has_dockerfile` | Section 8.11 | `true` / `false` |
| `has_docker_compose` | Section 8.11 | `true` / `false` |
| `ci_platform` | Section 8.8 | `GitHub Actions`, `GitLab CI` |
| `monorepo` | Section 8.5 | `true` / `false` |
| `naming_conventions` | Section 1.5 | list of `{scope, convention, example}` |
| `api_directory` | Glob for route patterns | `src/routes`, `app/api` |
| `migration_command` | Section 8.14 or ORM conventions | `npx prisma migrate dev` |

---

## 9.5 Hook Templates

Hooks are configured in `.claude/settings.json`. Generate only when **all three safety conditions** are met:

1. **Binary installed** — tool binary confirmed present (lockfile, config file, or `command -v`)
2. **Fast execution** — command runs in under 30 seconds
3. **Disable instructions** — hook includes a comment explaining how to disable it

If any condition is not met, do not generate the hook.

### Lint pre-commit hook

**Trigger:** Linter detected (section 8.7) AND linter binary confirmed installed.

```json
{
  "hooks": {
    "pre-commit": [
      {
        "command": "{{ lint_command }}",
        "comment": "Runs linter before each commit. To disable: remove this entry from .claude/settings.json"
      }
    ]
  }
}
```

### Lint + test pre-commit hook

**Trigger:** Linter detected AND test framework detected AND tests run in under 30 seconds.

```json
{
  "hooks": {
    "pre-commit": [
      {
        "command": "{{ lint_command }} && {{ test_command }}",
        "comment": "Runs linter and tests before each commit. To disable: remove this entry from .claude/settings.json"
      }
    ]
  }
}
```

### Available lifecycle events

| Event | When it fires |
|-------|--------------|
| `pre-commit` | Before `git commit` |
| `post-commit` | After `git commit` |
| `pre-push` | Before `git push` |

---

## 9.6 MCP Config Template

MCP servers extend Claude's capabilities. Generate `.mcp.json` when relevant tools are detected.

### Context7

**Trigger:** Framework detected that benefits from up-to-date documentation lookup (section 8.2).

**Frameworks that trigger Context7:** React, Next.js, Vue, Nuxt, Svelte, SvelteKit, Angular, Express, Fastify, NestJS, Django, FastAPI, Flask, Tailwind, Prisma, Drizzle.

```json
{
  "mcpServers": {
    "context7": {
      "command": "npx",
      "args": ["-y", "@anthropic/context7-mcp@latest"]
    }
  }
}
```

---

## 9.7 Selection Matrix

Quick-reference table mapping Phase 1 detections to Phase 2 outputs. Use this as the primary decision table.

| Detected | Commands | Skills | Agents | Hooks | MCP |
|----------|----------|--------|--------|-------|-----|
| Any project | commit, implement, fix, review | implement-feature, fix-bug, improve-architecture | architect | | |
| Frontend framework (styling) | | design-system | | | Context7* |
| Backend framework | security-audit | api-patterns | | | Context7* |
| Database / ORM | optimize-db | schema-patterns | db-specialist | | |
| Test framework | | tdd | qa, fixer | | |
| Linter OR test framework | | | reviewer | | |
| Test framework AND linter | | | developer | | |
| Docker OR CI/CD | | | devops | | |
| Linter | | | | lint pre-commit | |
| Linter + fast tests | | | | lint+test pre-commit | |

### Reading the matrix

- **Rows** are Phase 1 detections. Multiple rows can match simultaneously.
- **Columns** are Phase 2 output layers. Each cell lists what to generate.
- Empty cells mean nothing is generated for that combination.
- *Context7 is only generated for frameworks listed in section 9.6. Not all backend/frontend frameworks have Context7 documentation support.
- **Agent model selection:** Use `sonnet` for most agents. Use `opus` for `architect` (benefits from deeper reasoning).
- **Worktree isolation:** `developer` requires test framework AND linter. `fixer` requires test framework (linter is optional).
- **Stack-specific content:** All agents use `{{ if }}` conditionals to embed framework-specific knowledge. The same `developer.md` template produces React-specific output for a React project and Go-specific output for a Go project.

---

## 9.8 INSTRUCTION.md Template

After Phase 2 generates all `.claude/` configuration, produce an `INSTRUCTION.md` in the project root as a quick-start onboarding guide. This is tailored to what was actually generated — not a generic Claude Code tutorial.

### Rules

- Output must be under 150 lines
- Every section is conditional — only include sections for layers that were actually generated
- Content must reference the developer's actual project (stack names, framework versions, real command names)
- No `{{ placeholders }}` may remain in the final output
- Use a friendly, conversational tone — the reader just bootstrapped and should feel confident
- Include a note at the top: "This file was generated by project-architect. Feel free to edit or delete it."

### Template

`````markdown
<!-- This file was generated by project-architect. Feel free to edit or delete it. -->

# Getting Started with Your Claude Code Setup

{{ summary_paragraph }}

{{ if commands_generated }}

## Your Commands

| Command | What it does | Try it |
|---------|-------------|--------|
{{ for each generated command }}
| `/{{ command_name }}` | {{ one_line_description }} | `{{ example_usage }}` |
{{ end }}

{{ end }}

{{ if skills_generated }}

## Your Skills

Skills activate automatically when Claude detects relevant context. You don't need to invoke them — just ask naturally.

{{ for each generated skill }}
- **{{ skill_name }}** — {{ description }}. Try: "{{ natural_language_trigger }}"
{{ end }}

{{ end }}

{{ if agents_generated }}

## Your Agents

Agents run in separate context windows for tasks that benefit from focused expertise.

{{ for each generated agent }}
- **{{ agent_name }}** — {{ what_it_does }}. Invoke with: `{{ invocation_example }}`
{{ end }}

{{ end }}

{{ if hooks_generated }}

## Your Hooks

Hooks run automatically at specific lifecycle events. Each can be disabled individually.

{{ for each generated hook }}
- **{{ hook_event }}** — {{ what_it_does }}
  - To disable: remove the corresponding entry from `.claude/settings.json`
{{ end }}

{{ end }}

{{ if mcp_generated }}

## Your MCP Servers

External tools are now available to Claude through MCP:

{{ for each mcp_server }}
- **{{ server_name }}** — {{ what_it_provides }}
{{ end }}

{{ end }}

## Tips for Getting the Most Out of It

{{ for each stack_specific_tip }}
- {{ stack_specific_tip }}
{{ end }}

## Customizing Your Setup

- Edit `CLAUDE.md` to update project conventions or add new rules
- Add commands in `.claude/commands/` — any markdown file becomes a slash command
- Add skills in `.claude/skills/` — create a directory with a `SKILL.md` file
{{ if hooks_generated }}
- Review hooks in `.claude/settings.json` — remove any you don't want
{{ end }}
`````
