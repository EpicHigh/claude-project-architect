---
name: schema-patterns
description: >
  go-api-server database schema and data access patterns.
  Activates when: creating models, writing migrations, querying data,
  designing relationships, optimizing queries, managing database schema.
---

# Schema Patterns — go-api-server

## Stack

- **Database:** PostgreSQL
- **ORM:** Ent

## Conventions

- Find existing schemas with `Glob` and follow the same patterns.
- Check for naming conventions, relationship patterns, and field types.

## Migrations

```sh
go generate ./ent
```
