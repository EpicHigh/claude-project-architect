# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

python-fastapi is a Python web application using FastAPI.

## Build & Development

### Development Server

```sh
uvicorn app.main:app --reload
```

## Testing

### Run All Tests

```sh
pytest
```

### Run a Single Test

```sh
pytest tests/test_users.py
```

## Linting & Formatting

### Lint

```sh
ruff check .
```

### Format

```sh
ruff format .
```

## Working Style

Don't take shortcuts — read and explore before writing. Don't be lazy — produce thorough, complete output with proper error handling, validation, and tests.

- **Read existing router structure before adding endpoints** — Routes use `Depends(get_db)` for session injection; endpoints that create their own sessions bypass transaction management
- **Read existing Pydantic schemas in `schemas/` before creating new ones** — This project shares schemas across endpoints; duplicating instead of reusing causes validation drift
- **Run `pytest` before and after changes** — You cannot distinguish pre-existing failures from regressions without a baseline
- **Check Alembic migration state before modifying models** — Generating a migration against an outdated state creates branching conflicts in the migration chain
- **Read `app/dependencies.py` before adding dependency injection** — This project centralizes shared dependencies; duplicating them in individual routers creates inconsistent behavior
- **Implement complete request/response schemas with validation** — An endpoint with `dict` input instead of a Pydantic model bypasses all validation; every endpoint needs typed schemas in `schemas/`
- **Write tests that verify response body structure, not just status codes** — A test that only checks `assert response.status_code == 200` misses every data bug; verify the JSON body matches the Pydantic response schema

## Database

- **Engine:** PostgreSQL
- **ORM:** SQLAlchemy
- **Migrations:** `alembic revision --autogenerate -m "add_users_table"`
