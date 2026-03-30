# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

go-api-server is a Go web application using Gin.

## Build & Development

### Build

```sh
make build
```

### Development Server

```sh
make run
```

## Testing

### Run All Tests

```sh
make test
```

### Run a Single Test

```sh
go test ./internal/handler/...
```

## Linting & Formatting

### Lint

```sh
make lint
```

## Working Style

Don't take shortcuts — read and explore before writing. Don't be lazy — produce thorough, complete output with proper error handling, validation, and tests.

- **Run `go generate ./ent` after schema changes, then read generated types** — The generated API in `ent/` changes after every schema modification; coding against stale types causes compile errors
- **Read existing middleware chain before adding handlers** — Routes inherit middleware from parent `r.Group()` in Gin; re-registering the same middleware on a child group causes it to run twice for that group's routes
- **Run `make test` before and after changes** — You cannot distinguish pre-existing failures from regressions without a baseline
- **Read `internal/handler/` patterns before adding endpoints** — This project follows a consistent handler → service → repository layering; skipping the service layer couples handlers directly to Ent queries
- **Check Ent migration state before modifying schemas** — Generating a migration against an outdated state creates branching conflicts in the migration chain
- **Implement complete handler → service → repository for each endpoint** — A handler that calls Ent directly skips the service layer and makes the endpoint untestable; every endpoint needs all three layers
- **Return structured error responses, not raw Go errors** — An endpoint that returns `err.Error()` as plain text exposes internals; use the project's error response format in `internal/handler/response.go`

## Database

- **Engine:** PostgreSQL (via Ent ORM)
- **ORM:** Ent
- **Migrations:** `go generate ./ent`
