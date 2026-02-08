# create-project-agency

> **Note:** `CLAUDE.md` is a symlink to this file.

## Overview

Bootstrap an `AGENTS.md` and documentation index for any project. Auto-detects tech stack, framework, testing tools, and project structure from the filesystem, then generates structured project documentation through guided questions.

## Structure

```
create-project-agency/
├── SKILL.md                         # Main skill (workflow + detection logic)
├── AGENTS.md                        # Navigation guide (this file)
├── CLAUDE.md                        # Symlink → AGENTS.md
├── README.md                        # GitHub-facing documentation
├── LICENSE                          # MIT
└── references/
    └── claude-md-template.md        # Annotated template with all sections
```

## Usage

1. Read `SKILL.md` for the full workflow instructions (Phases 0–6)
2. Browse `references/claude-md-template.md` for the canonical CLAUDE.md format
3. Invoke via `/create-project-agency`
4. Pass `--update` to update an existing AGENTS.md instead of regenerating

## How It Works

1. **Phase 0** — Asks about CLAUDE.md symlink, then checks for existing AGENTS.md
2. **Phase 1** — Silently scans the project for package managers, frameworks, monorepo tools, testing, styling, backend, and documentation
3. **Phase 2** — Presents detected profile, asks targeted questions, confirms tech stack for doc fetching
4. **Phase 3** — Fetches documentation for selected technologies via Context7 MCP, saves to `.docs/`
5. **Phase 4** — Generates AGENTS.md with tech docs index using the template from `references/`
6. **Phase 5** — Shows output for review and iteration
7. **Phase 6** — Offers to create supporting docs (architecture, testing, vision, contributing, PLAN.md)
