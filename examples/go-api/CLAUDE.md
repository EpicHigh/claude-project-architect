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

- **Run `go generate ./ent` after schema changes, then read generated types** — The generated API in `ent/` changes after every schema modification; coding against stale types causes compile errors
- **Read existing middleware chain before adding handlers** — Routes inherit middleware from parent `r.Group()` in Gin; adding duplicate auth/logging creates double-execution bugs
- **Run `make test` before and after changes** — You cannot distinguish pre-existing failures from regressions without a baseline
- **Read `internal/handler/` patterns before adding endpoints** — This project follows a consistent handler → service → repository layering; skipping the service layer couples handlers directly to Ent queries

## Database

- **Engine:** PostgreSQL (via Ent ORM)
- **ORM:** Ent
- **Migrations:** `go generate ./ent`
