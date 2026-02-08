# create-project-agency

Bootstrap an `AGENTS.md` and documentation index for any project. Detects your tech stack automatically and generates structured project documentation through guided questions.

## Installation

```bash
npx skills add im-ironclad/create-project-agency-skill
```

## What It Does

When you run `/create-project-agency` in Claude Code, the skill:

1. **Scans your project** — detects package manager, framework, monorepo setup, testing tools, styling, backend, and existing documentation
2. **Presents findings** — shows what it detected and asks targeted questions to fill gaps
3. **Fetches tech stack docs** — pulls real documentation for your detected technologies via Context7 MCP and saves them locally to `.docs/`
4. **Generates AGENTS.md** — creates a structured, project-specific documentation file with a tech docs index, following a proven template, optionally with a `CLAUDE.md` symlink
5. **Iterates with you** — shows the output for review before writing
6. **Creates supporting docs** — optionally generates architecture, testing, vision, contributing, and implementation plan docs

## What It Generates

- **AGENTS.md** — project header, tech stack, structure, conventions, tech docs index, doc index (under 80 lines)
- **CLAUDE.md** — optional symlink to AGENTS.md (for Claude Code compatibility)
- **`.docs/{technology}/`** — fetched documentation per technology (e.g., `.docs/nextjs/routing.md`)
- **docs/architecture.md** — tech stack rationale, domain model, data flows, deployment
- **docs/testing.md** — philosophy, conventions, file naming, test data strategy
- **docs/vision.md** — problem statement, target audience, differentiators
- **docs/contributing.md** — setup steps, PR process, code review expectations
- **PLAN.md** — implementation phases, decisions, verification checklist

## Supported Detections

| Category | Signals |
|----------|---------|
| **Package Manager** | yarn, npm, pnpm, bun, pip, go mod, cargo, gem, composer |
| **Framework** | Next.js, Nuxt, SvelteKit, Vite, Angular, Remix, Astro |
| **Monorepo** | Turborepo, Nx, pnpm workspaces, yarn workspaces, Lerna |
| **Testing** | Vitest, Jest, Playwright, Cypress, pytest |
| **Styling** | TailwindCSS (v3 + v4), PostCSS, CSS Modules |
| **Backend/DB** | Convex, Supabase, Prisma, Drizzle, Firebase |
| **Linting** | ESLint, Prettier, Biome |
| **Language** | TypeScript (strict mode detection), JavaScript, Python, Go, Rust, Ruby, PHP |

## Usage

```
/create-project-agency           # Generate from scratch or update existing
/create-project-agency --update  # Skip prompt, go straight to update mode
```

The skill creates `AGENTS.md` by default and asks if you'd also like a `CLAUDE.md` symlink pointing to it.

## Tech Stack Documentation

The skill uses **retrieval-led reasoning** — after detecting your tech stack, it fetches real documentation for each technology via [Context7 MCP](https://github.com/upstash/context7) and saves it locally to `.docs/{technology}/`. This means the agent consults actual, up-to-date docs rather than relying on training data that may be outdated.

Each technology gets up to 3 targeted documentation files covering core topics (routing, data fetching, configuration, etc.). The generated AGENTS.md includes a compressed index pointing to these files, so the agent always knows where to look.

If Context7 MCP is not available, the skill offers fallback options: skip tech docs, provide URLs for manual fetching, or point to local documentation.

## Update Mode

If an `AGENTS.md` already exists, the skill can update it incrementally — detecting new tools, directory changes, and missing doc index entries without regenerating from scratch. If only a `CLAUDE.md` exists, the skill offers to migrate it to `AGENTS.md` with a symlink.

## License

MIT
