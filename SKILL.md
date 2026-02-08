---
name: create-project-agency
description: >
  Bootstrap a CLAUDE.md and documentation index for any project. Detects
  project signals from the filesystem, asks targeted questions, and generates
  structured project documentation. Use when setting up Claude Code for a new
  project or updating an existing CLAUDE.md.
disable-model-invocation: true
license: MIT
metadata:
  author: ironcladapps
  version: "1.0.0"
  organization: Ironclad Apps
  abstract: >
    Project documentation bootstrapper that auto-detects tech stack, framework,
    testing tools, and project structure, then generates a CLAUDE.md and
    supporting docs (architecture, testing, vision, contributing, PLAN.md)
    through guided questions.
---

# Create Project Agency

Generate a `CLAUDE.md` and supporting documentation for the current project. Detects framework, tooling, and structure automatically, then asks targeted questions to fill gaps.

**Arguments**: `$ARGUMENTS`
- `--update` — Skip the existing-file prompt and go straight to update mode

---

## Phase 0: Existing File Check

Check if `CLAUDE.md` already exists in the project root.

**If it exists AND `--update` was NOT passed:**
- Show the user a summary of the existing file
- Ask: "CLAUDE.md already exists. Would you like to: (1) Update it with new detections, (2) Regenerate from scratch, (3) Cancel?"
- If "Update" → proceed to Phase 1 in **update mode** (see Update Mode section below)
- If "Regenerate" → proceed to Phase 1 normally (will overwrite)
- If "Cancel" → stop

**If it exists AND `--update` was passed:**
- Proceed directly to Phase 1 in **update mode**

**If it does not exist:**
- Proceed to Phase 1 normally

---

## Phase 1: Silent Detection

Scan the project root to build a detection profile. Do NOT ask the user anything yet — just gather signals. Use Glob and Read tools.

### What to Detect

**Package ecosystem** — check for these files at the project root:
- `package.json` → read `name`, `description`, `scripts`, `dependencies`, `devDependencies`, `workspaces`, `packageManager`
- Lockfiles: `yarn.lock` → yarn, `package-lock.json` → npm, `pnpm-lock.yaml` → pnpm, `bun.lock` / `bun.lockb` → bun
- `requirements.txt` / `pyproject.toml` / `setup.py` → Python
- `go.mod` → Go
- `Cargo.toml` → Rust
- `Gemfile` → Ruby
- `composer.json` → PHP

**Framework** — check for config files:
- `next.config.*` → Next.js (read for App Router vs Pages Router signals)
- `nuxt.config.*` → Nuxt
- `svelte.config.*` → SvelteKit
- `vite.config.*` → Vite
- `angular.json` → Angular
- `remix.config.*` → Remix
- `astro.config.*` → Astro

**TypeScript** — `tsconfig.json`:
- Check `compilerOptions.strict` for strict mode
- Check `compilerOptions.paths` for path aliases

**Monorepo** — check for:
- `turbo.json` → Turborepo
- `nx.json` → Nx
- `pnpm-workspace.yaml` → pnpm workspaces
- `workspaces` field in root `package.json` → yarn/npm workspaces
- `lerna.json` → Lerna

**Linting / formatting**:
- `.eslintrc*`, `eslint.config.*` → ESLint
- `.prettierrc*`, `prettier.config.*` → Prettier
- `biome.json`, `biome.jsonc` → Biome

**Testing**:
- `vitest.config.*` or `vitest` in devDeps → Vitest
- `jest.config.*` or `jest` in devDeps → Jest
- `playwright.config.*` or `@playwright/test` in devDeps → Playwright
- `cypress.config.*` or `cypress` in devDeps → Cypress
- `pytest.ini`, `conftest.py`, `pyproject.toml [tool.pytest]` → pytest

**Styling**:
- `tailwind.config.*` or `@tailwindcss` in deps → Tailwind CSS (check v3 config file vs v4 CSS-only)
- `postcss.config.*` → PostCSS

**Backend / database**:
- `convex/` directory → Convex
- `supabase/` directory or `supabase` in deps → Supabase
- `prisma/` directory → Prisma
- `drizzle.config.*` → Drizzle
- `firebase.json` or `firebaserc` → Firebase

**Existing documentation**:
- `README.md` → read for project description and setup instructions
- `docs/` directory → list contents
- Any root `.md` files → note them

**Directory structure**:
- List top-level directories (exclude: `node_modules`, `.git`, `dist`, `build`, `.next`, `.turbo`, `__pycache__`, `.venv`, `target`, `vendor`)
- For monorepos, also list workspace directories one level deep

### Detection Output Format

Build an internal profile object (not shown to user yet):

```
Project: {name from package.json or directory name}
Description: {from package.json or README}
Package Manager: {detected}
Language: {TypeScript strict | TypeScript | JavaScript | Python | Go | Rust | ...}
Framework: {detected}
Monorepo: {tool} with workspaces: [{list}]
Styling: {detected}
Backend: {detected}
Testing: {tools detected}
Linting: {tools detected}
Structure: {top-level dirs}
Existing Docs: {list of .md files found}
```

---

## Phase 2: Present Findings & Ask Questions

Show the detection summary in a clean format:

```
## Detected Project Profile

- **Project**: {name} — {description or "no description found"}
- **Package Manager**: {yarn | npm | pnpm | bun | pip | ...}
- **Language**: {TypeScript (strict) | TypeScript | JavaScript | ...}
- **Framework**: {Next.js (App Router) | Nuxt 3 | SvelteKit | ...}
- **Monorepo**: {Turborepo | Nx | none} — workspaces: {list}
- **Styling**: {TailwindCSS v4 | TailwindCSS v3 | CSS Modules | ...}
- **Backend**: {Convex | Supabase | Prisma + PostgreSQL | ...}
- **Testing**: {Vitest + Playwright | Jest + Cypress | ...}
- **Linting**: {ESLint + Prettier | Biome | ...}
- **Structure**: {key directories}
```

Then ask targeted questions using AskUserQuestion. Only ask what detection couldn't determine:

1. **Always ask**: "Is this detection correct? Anything to add or change?" (freeform)
2. **If no description found**: "What is this project in one sentence?"
3. **Always ask**: "Any hard constraints Claude should always follow?" (e.g., "never use npm", "always prefer server components", "use conventional commits")
4. **Always ask**: "Any conventions not captured above?" (e.g., naming patterns, file organization rules, component patterns)

Keep questions minimal. If detection was thorough, questions 1 and 3 may be sufficient.

---

## Phase 3: Generate CLAUDE.md

Write the file using the template structure from `references/claude-md-template.md`. Read that file now for the exact format.

### Section Order (strict)

1. **Project header** — `# Name` + one-line description
2. **Tech Stack** — bulleted list: `- **Category**: Technology`
3. **Project Structure** — fenced ASCII tree, 1-2 levels deep. Add inline comments for non-obvious directories. Include "Future additions" line if the user mentioned planned directories.
4. **Conventions** — concrete, actionable rules. Use sub-sections (e.g., `### Testing`, `### Naming`) when a category has 3+ rules.
5. **Implementation Plan** (optional) — only if `PLAN.md` exists. Brief pointer to the file.
6. **Doc Index** — compressed pipe-delimited format. Only list files that actually exist. Always include the index maintenance rule.

### Writing Rules

- **Be concrete, not generic.** "Use `yarn` for all package management" is good. "Follow best practices" is not.
- **Omit sections with nothing to say.** If there's no monorepo, don't include a structure section showing a flat project.
- **Use the user's words** from their answers in Phase 2 for constraints and conventions.
- **Keep it under 80 lines.** CLAUDE.md should be scannable. Link to docs for details.
- **No placeholder content.** Every line should be real information about this project.

---

## Phase 4: Verify & Iterate

Show the generated CLAUDE.md content to the user. Ask:

"Here's the generated CLAUDE.md. Would you like any adjustments before I write it?"

If the user requests changes, make them. When approved:
1. Write the file
2. Remind: "Don't forget to commit `CLAUDE.md` to your repository."

---

## Phase 5: Guided Documentation Creation

After CLAUDE.md is finalized, offer to create supporting documentation.

### Presenting the Menu

Show only doc types that make sense for the detected project:

| Doc | When to Offer | File Path |
|-----|--------------|-----------|
| Architecture | Always (non-trivial projects) | `docs/architecture.md` |
| Testing | When testing tools were detected | `docs/testing.md` |
| Vision | When user describes an ambitious product | `docs/vision.md` |
| Contributing | When project likely has collaborators (GitHub remote, multiple contributors in git log) | `docs/contributing.md` |
| Implementation Plan | When project is in active development | `PLAN.md` |

Present as a selectable menu:

"Would you like to create any supporting documentation? Select all that apply, or skip to finish."

- Architecture (recommended for most projects)
- Testing conventions
- Product vision
- Contributing guide
- Implementation plan (PLAN.md)
- Skip — I'm done

### Flow for Each Selected Doc

For each doc the user selects:

**1. Ask targeted questions** (3-5, tailored to what was detected):

**Architecture (`docs/architecture.md`):**
- "What's the rationale behind your tech stack choices?" (pre-fill from detection)
- "Describe your core domain model — what are the key entities and their relationships?"
- "What are the main data flows? (e.g., user action → API → database → real-time update)"
- "How is this deployed? (Vercel, AWS, self-hosted, etc.)"
- "Any planned architectural changes or future components?"

**Testing (`docs/testing.md`):**
- "What's your testing philosophy? (e.g., test as user stories, test pyramid, TDD)"
- "File naming conventions for tests?" (pre-fill if detected: `*.test.ts`, `*.spec.ts`)
- "Any test data conventions? (factories, fixtures, test email domains, cleanup strategy)"
- "What should be unit tested vs E2E tested?"
- "Any testing-specific tooling? (MSW, test containers, custom fixtures)"

**Vision (`docs/vision.md`):**
- "What problem does this project solve?"
- "Who is the target audience?"
- "What differentiates this from alternatives?"
- "What's the core UX philosophy?"

**Contributing (`docs/contributing.md`):**
- "What are the steps to set up the dev environment from scratch?"
- "What's the PR process? (branch naming, review requirements, merge strategy)"
- "Any code review expectations?"
- "Required checks before merging? (tests, linting, type-check)"

**Implementation Plan (`PLAN.md`):**
- "What phase is the project in now?"
- "What's been completed so far?"
- "What are the next 3-5 steps?"
- "Any key decisions already made that should be recorded?"

**2. Generate the doc** from answers + detected signals. Follow the same writing rules as Phase 3: concrete, not generic. Use the template examples as quality benchmarks — read them from `references/claude-md-template.md` for format guidance.

**3. Show output and ask for adjustments.**

**4. Write the file** and update the Doc Index in CLAUDE.md:
- Add a new entry in the pipe-delimited format
- Entry format: `|{key}:{{file path} - {compressed single-line summary}}`

**5. Move to the next selected doc** or finish.

### After All Docs Are Created

Show a summary of everything that was created:

```
## Created files:
- CLAUDE.md
- docs/architecture.md
- docs/testing.md
(etc.)

Remember to commit these files to your repository.
```

---

## Update Mode

When running in update mode (existing CLAUDE.md + `--update` flag or user chose "Update"):

1. **Run Phase 1 detection** as normal
2. **Read current CLAUDE.md** content
3. **Diff detection against current content** — identify:
   - New technologies/tools detected but not in CLAUDE.md
   - Directory structure changes
   - New documentation files not in the Doc Index
4. **Present proposed changes** to the user:
   ```
   ## Proposed Updates to CLAUDE.md

   **Add to Tech Stack:**
   - Vitest (detected in devDependencies)

   **Update Structure:**
   - New directory: `apps/mobile/`

   **Update Doc Index:**
   - Add: docs/contributing.md

   **No changes to:**
   - Conventions (still accurate)
   - Project description
   ```
5. **Ask for approval** — user can accept all, pick specific changes, or cancel
6. **Apply approved changes** — edit the file, don't regenerate from scratch
7. **Never remove content without asking** — if something in CLAUDE.md wasn't detected, ask before removing: "I didn't detect {X} anymore. Should I remove it from CLAUDE.md?"

---

## Important Guidelines

- **Use Glob and Read tools** for all file detection — never guess what exists
- **Read `references/claude-md-template.md`** before generating any CLAUDE.md content — it contains the canonical format and examples
- **Don't fabricate information** — if you can't detect something and the user didn't mention it, leave it out
- **Keep CLAUDE.md under 80 lines** — it's a quick reference, not exhaustive documentation
- **The Doc Index uses pipe-delimited format** — match the exact format shown in the template
- **Always create the `docs/` directory** before writing files into it
- **Update CLAUDE.md's Doc Index** every time a new doc is created
