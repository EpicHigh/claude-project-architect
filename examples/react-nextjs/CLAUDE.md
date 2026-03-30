# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

react-next-app is a TypeScript web application using Next.js 14.

## Build & Development

### Build

```sh
npm run build
```

### Development Server

```sh
npm run dev
```

## Testing

### Run All Tests

```sh
npm test
```

### Run a Single Test

```sh
npx jest path/to/test
```

### Coverage

```sh
npm run test:coverage
```

## Linting & Formatting

### Lint

```sh
npm run lint
```

## Working Style

- **Read component boundary before adding data access** — Prisma queries belong in Server Components or server actions (`app/` files without `'use client'`); the bundler fails because Prisma depends on Node.js APIs unavailable in the browser
- **Check `app/` route structure before creating files** — `page.tsx`, `layout.tsx`, `loading.tsx` have special meanings in App Router; misnamed files silently break routing
- **Run `npm test` before and after changes** — Jest tests use the project's custom config; you cannot distinguish pre-existing failures from regressions without a baseline
- **Read existing Prisma schema before adding models** — `prisma/schema.prisma` has established relation patterns; inconsistent relations cause migration failures
- **Check `components/ui/` before building custom UI** — This project uses shadcn/ui primitives; duplicating existing components creates visual inconsistency

## Database

- **Engine:** PostgreSQL
- **ORM:** Prisma
- **Migrations:** `npx prisma migrate dev`
