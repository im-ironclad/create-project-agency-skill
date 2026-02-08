# CLAUDE.md Template Reference

This is an annotated template showing the canonical CLAUDE.md format. When generating a CLAUDE.md, follow this structure exactly. Guidance comments (lines starting with `>`) should NOT appear in the output.

---

## Section 1: Project Header

> Format: `# ProjectName` followed by a one-line description.
> The description should say what the project IS plus who it's for or what makes it distinctive.

```markdown
# ProjectName

One-line description of the project — what it is and what makes it distinctive.
```

**Example:**
```markdown
# Acme App

Task management app for distributed teams — real-time collaboration with offline support.
```

---

## Section 2: Tech Stack

> Bulleted list. Each item: `- **Category**: Technology (rationale if not obvious)`
> Only include categories that are meaningful. Don't list every devDependency.
> Order: infrastructure first (monorepo, hosting), then framework, backend, styling, package manager.

```markdown
## Tech Stack

- **Monorepo**: Turborepo
- **Framework**: Next.js (App Router) + TypeScript
- **Backend/DB**: Supabase (PostgreSQL + real-time subscriptions)
- **Styling**: TailwindCSS
- **Package Manager**: pnpm (never npm)
```

> Include parenthetical constraints like "(never npm)" when the user has strong preferences.
> Combine related items: "Next.js (App Router) + TypeScript" rather than separate lines.

---

## Section 3: Project Structure

> Fenced ASCII tree, 1-2 levels deep. Use inline comments for non-obvious directories.
> Section heading should describe the structure type (e.g., "Monorepo Structure", "Project Structure").
> Include a "Future additions" note if the user mentioned planned directories.

```markdown
## Monorepo Structure

\```
acme-app/
├── apps/
│   └── web/          # Next.js web application (primary interface)
├── packages/
│   └── shared/       # Shared types, utilities, schema
├── turbo.json
└── package.json      # Root workspace config
\```

Future additions: `apps/mobile` (React Native dashboard)
```

> Skip this section entirely for flat projects where the structure is obvious.
> For non-monorepo projects, use "Project Structure" as the heading.

---

## Section 4: Conventions

> Concrete, actionable rules. Each rule should tell Claude what to DO or NOT DO.
> Use sub-sections (`### SubTopic`) when a category has 3+ rules.
> Rules should be specific to this project, not generic best practices.

```markdown
## Conventions

- Use `pnpm` for all package management
- TypeScript strict mode
- Prefer server components; use `"use client"` only when necessary
- Colocate components with their routes when possible
- Use Supabase client for all data operations

### Testing

- **Vitest** for unit tests, **Playwright** for E2E — see `docs/testing.md`
- Write tests as user stories; use Page Object Model for E2E
- E2E tests import from `e2e/fixtures/test-base.ts`, never from `@playwright/test` directly
- Test emails use `@acme-test.local` domain
```

> Good rules: "Use `pnpm` for all package management" — specific, actionable
> Bad rules: "Follow best practices" — vague, useless
> Bad rules: "Write clean code" — subjective, not actionable
> Only create sub-sections when there are 3+ rules for a topic. Don't create a sub-section for 1-2 rules.

---

## Section 5: Tech Stack Docs (optional)

> Only include if tech documentation was fetched in Phase 3.
> This section provides a compressed index of framework/library documentation stored locally.
> The index format mirrors the Doc Index but points to `.docs/` instead of `./docs/`.

```markdown
## Tech Stack Docs

Prefer retrieval-led reasoning over pre-training-led reasoning for any tech stack tasks. Consult the relevant documentation below before implementing features or making technical decisions.

\```
[ProjectName Tech Docs]|root: ./.docs
|nextjs:{routing.md, data-fetching.md, api-routes.md}
|typescript:{type-system.md, configuration.md, patterns.md}
\```
```

> Index key: lowercase technology name (nextjs, typescript, tailwindcss, vitest, prisma, etc.)
> Entries list actual filenames saved in `.docs/{technology}/`
> Only include entries for technologies that have docs fetched
> This section doesn't count toward the 80-line target — it's a machine-generated reference

---

## Section 6: Implementation Plan (optional)

> Only include if PLAN.md exists or will be created.
> This is a pointer, not a duplicate of the plan content.

```markdown
## Implementation Plan

The active implementation plan lives in `PLAN.md` at the project root. **Always read `PLAN.md` before starting work** to understand current progress, next steps, and decisions already made. Update it as you complete steps — check off items, note deviations, and add new phases as needed.
```

---

## Section 7: Doc Index

> Compressed pipe-delimited format. Only list files that actually exist.
> The index name should be the project name.
> Each entry: `|key:{filepath - compressed single-line summary}`
> Always end with the maintenance rule.

```markdown
## Doc Index

Prefer retrieval-led reasoning over assumption-led reasoning. Consult the Doc Index and read relevant docs before making architectural or product decisions.

\```
[ProjectName Docs Index]|root: ./
|plan:{PLAN.md - active implementation plan, current phase, step checklist, file structure, env vars, verification}
|vision:{docs/vision.md - product vision, target audience, progressive disclosure UX, differentiators}
|architecture:{docs/architecture.md - tech stack, domain model, data flows, deployment, future architecture}
|testing:{docs/testing.md - testing philosophy, framework stack, conventions, file naming, data strategy}
\```

**Index maintenance rule**: When creating or significantly modifying any documentation file, always update the Doc Index in this file to reflect the changes. Keep index entries as compressed single-line summaries.
```

> The index key is a short identifier (plan, vision, architecture, testing, contributing).
> The summary after the dash should list the key topics covered in that doc, comma-separated.
> Only include entries for files that exist. Don't add placeholders.

---

## Full Example

This is a complete, production-quality CLAUDE.md — use it as the quality benchmark:

```markdown
# Acme App

Task management app for distributed teams — real-time collaboration with offline support.

## Tech Stack

- **Monorepo**: Turborepo
- **Framework**: Next.js (App Router) + TypeScript
- **Backend/DB**: Supabase (PostgreSQL + real-time subscriptions)
- **Styling**: TailwindCSS
- **Package Manager**: pnpm (never npm)

## Monorepo Structure

\```
acme-app/
├── apps/
│   └── web/          # Next.js web application (primary interface)
├── packages/
│   └── shared/       # Shared types, utilities, database schema
├── turbo.json
└── package.json      # Root workspace config
\```

Future additions: `apps/mobile` (React Native dashboard)

## Conventions

- Use `pnpm` for all package management
- TypeScript strict mode
- Prefer server components; use `"use client"` only when necessary
- Colocate components with their routes when possible
- Use Supabase client for all data operations

### Testing

- **Vitest** for unit tests, **Playwright** for E2E — see `docs/testing.md`
- Write tests as user stories; use Page Object Model for E2E
- E2E tests import from `e2e/fixtures/test-base.ts`, never from `@playwright/test` directly
- Test emails use `@acme-test.local` domain

## Tech Stack Docs

Prefer retrieval-led reasoning over pre-training-led reasoning for any tech stack tasks. Consult the relevant documentation below before implementing features or making technical decisions.

\```
[Acme App Tech Docs]|root: ./.docs
|nextjs:{routing.md, data-fetching.md, api-routes.md}
|typescript:{type-system.md, configuration.md, patterns.md}
|tailwindcss:{configuration.md, utilities.md, theming.md}
\```

## Implementation Plan

The active implementation plan lives in `PLAN.md` at the project root. **Always read `PLAN.md` before starting work** to understand current progress, next steps, and decisions already made. Update it as you complete steps — check off items, note deviations, and add new phases as needed.

## Doc Index

Prefer retrieval-led reasoning over assumption-led reasoning. Consult the Doc Index and read relevant docs before making architectural or product decisions.

\```
[Acme App Docs Index]|root: ./
|plan:{PLAN.md - active implementation plan, current phase, step checklist, file structure, env vars, verification}
|vision:{docs/vision.md - product vision, target audience, real-time collaboration UX, differentiators vs Asana/Linear}
|architecture:{docs/architecture.md - tech stack, domain model, task execution flow, Supabase real-time, mobile dashboard vision}
|testing:{docs/testing.md - testing philosophy, Vitest/Playwright stack, E2E conventions, Page Object Model, test data strategy}
\```

**Index maintenance rule**: When creating or significantly modifying any documentation file, always update the Doc Index in this file to reflect the changes. Keep index entries as compressed single-line summaries.
```

---

## Supporting Doc Format Guidelines

When generating docs in Phase 6, follow these patterns:

### Architecture Doc (`docs/architecture.md`)
- Start with a tech stack table (Layer | Technology | Rationale)
- Include core domain model as ASCII diagram
- Show key data flows as ASCII diagrams
- Section for real-time / deployment specifics
- Section for future/planned architecture

### Testing Doc (`docs/testing.md`)
- Start with philosophy statement
- Stack table (Tool | Purpose | Location)
- Running tests section with exact commands
- Conventions with sub-sections per test type (E2E, Unit)
- File naming, page objects, fixtures, test data strategy
- "What to test" and "What NOT to test" guidance

### Vision Doc (`docs/vision.md`)
- Problem statement
- Vision statement
- Core UX philosophy (e.g., progressive disclosure)
- Differentiator table (Feature | Alternatives | This Project)
- Target audience tiers (Primary, Secondary, Tertiary)

### Contributing Doc (`docs/contributing.md`)
- Prerequisites and setup steps (exact commands)
- Development workflow (branch, code, test, PR)
- Code review expectations
- Required CI checks

### Implementation Plan (`PLAN.md`)
- Status line at top with current phase
- Decisions Made section
- Phases with checkable steps
- Deviations noted inline
- Target file structure
- Environment variables
- Verification checklist
- Future phases (unplanned)
