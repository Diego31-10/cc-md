---
name: cc-md
description: Initialize or re-scan a project with persistent memory. Run this once per project to set up the memory vault and generate a dynamic CLAUDE.md. After activation, memory updates happen proactively during every session.
---

# CC.md — Project Memory Activation

## When to use this skill

- User types `/cc-md` in any project
- User asks to "activate cc-md", "setup memory", or "initialize cc-md"

## Pre-flight check

Before starting, check if `.cc-md.json` exists in the current working directory:

```bash
ls .cc-md.json 2>/dev/null && echo "EXISTS" || echo "NOT_FOUND"
```

**If EXISTS:** Ask the user:
> "CC.md is already active in this project (activated on {{activatedAt}}). Do you want to:
> A) Re-scan — refresh tech_architecture.md and MEMORY.md with current project state
> B) Exit — keep current memory as-is"

If user chooses B → stop here.
If user chooses A → skip to Step 3 (Scan), apply re-scan merge policy.

**If NOT_FOUND:** Continue to Step 1.

---

## Step 1 — Detect project root and compute hash

Get the absolute path of the current working directory:

```bash
pwd
```

Compute the project hash from the path using this logic:
- Normalize separators to `/`
- If path starts with `/mnt/` (WSL) → `DRIVE--rest-of-path`
- If path matches `/[a-z]/` (Git Bash) → `DRIVE--rest-of-path`
- If path matches `[A-Z]:` (Windows native) → `DRIVE--rest-of-path`
- If path starts with `/` (Unix) → strip leading slash
- Replace all `/` and `\` with `-`, collapse multiple `-` into `--`

Example: `/home/user/projects/my-app` → `home-user-projects-my-app`
Example: `C:/Users/diego/projects/my-app` → `C--Users-diego-projects-my-app`

The memory directory will be: `~/.claude/projects/{{HASH}}/memory/`

---

## Step 2 — Scan sentinel files

Read only these files — do not traverse subdirectories or read source code:

**Always read if present:**
```bash
ls -la          # top-level directory structure
```

**Read if exists:**
- `package.json` — extract: name, description, dependencies, devDependencies, scripts
- `requirements.txt` or `pyproject.toml` — extract: package names
- `Cargo.toml` — extract: name, dependencies
- `go.mod` — extract: module name, require block
- `README.md` — first 50 lines only
- `tsconfig.json` — extract: compilerOptions.paths, target, strict
- `docker-compose.yml` — extract: services names
- `.env.example` — extract: variable names (not values)
- `Makefile` — extract: target names
- `.github/workflows/*.yml` — extract: job names (first file only)

**Always run:**
```bash
git log --oneline -20 2>/dev/null || echo "No git history"
git branch -a 2>/dev/null | head -10 || echo "No branches"
```

**If no README.md and no package.json/requirements/Cargo.toml/go.mod found:**
- Identify the 3 most likely entry point files from `ls` output (e.g., `main.py`, `index.js`, `app.js`, `server.ts`, `main.go`)
- Read those 3 files (first 40 lines each)
- Ask the user ONE question before continuing:
  > "I can see the project structure but there's no documentation. What does this project do and what's your current objective?"
- Wait for the answer before proceeding to Step 3.

---

## Step 3 — Populate memory vault with intelligence

Create the memory directory:
```bash
mkdir -p ~/.claude/projects/{{HASH}}/memory
```

Using the data collected from the scan, write each file with real content — not placeholders. Apply your understanding of the project to fill in meaningful information.

**`tech_architecture.md`** — Write a complete technical picture:
- Detected stack with versions where available
- Key dependencies grouped by purpose (UI, DB, testing, tooling, etc.)
- Directory structure with purpose of each top-level folder
- Inferred patterns (e.g., "uses REST API", "monorepo", "microservices", "MVC")
- Environment variables detected (names only, not values)

**`project_state.md`** — Write the current state:
- Project name and one-line description
- Phase: "New project (no commits)" OR "Active development (N commits)" OR "Mature project"
- Current objective: infer from README/recent commits OR use the user's answer from Step 2
- Next step: leave as "(to be defined in first session)" if unknown

**`learnings.md`** — Write structure only, content empty:
- Include the header and section titles
- Add one example entry if git log shows any obvious bug fixes (commits with "fix:" prefix)

**`decisions.md`** — Write structure only, content empty:
- Include the header, ADR template, and section titles
- Add one ADR entry if there's a clear architectural decision visible (e.g., "chose PostgreSQL over MongoDB")

**`MEMORY.md`** — Write the index with correct absolute paths to all 4 files above.

**Re-scan merge policy** (when `.cc-md.json` already exists):
- `tech_architecture.md` → overwrite completely with fresh scan data
- `MEMORY.md` → overwrite completely
- `project_state.md` → preserve existing tasks/progress, update stack section only
- `learnings.md` → preserve all existing content, do not touch
- `decisions.md` → preserve all existing content, do not touch

---

## Step 4 — Create `.cc-md.json`

Write `.cc-md.json` in the project root:

```json
{
  "version": "1.0.0",
  "projectPath": "{{ABSOLUTE_PATH}}",
  "projectHash": "{{HASH}}",
  "activatedAt": "{{ISO_DATE}}",
  "lastScan": "{{ISO_DATE}}",
  "memoryDir": "~/.claude/projects/{{HASH}}/memory"
}
```

---

## Step 5 — Generate CLAUDE.md

Generate `CLAUDE.md` in the project root using the template from `templates/CLAUDE.md` inside the cc-md plugin directory.

Replace all `{{PLACEHOLDERS}}` with real values:
- `{{PROJECT_NAME}}` → project name from package.json/README/directory name
- `{{MEMORY_DIR}}` → `~/.claude/projects/{{HASH}}/memory`
- `{{STACK}}` → detected stack (e.g., "Next.js 14 + TypeScript + PostgreSQL")
- `{{PHASE}}` → inferred phase
- `{{CURRENT_OBJECTIVE}}` → from README or user's answer
- `{{CONSTRAINTS}}` → any constraints inferred from the project (e.g., "Node >= 18", "PostgreSQL required")

**If CLAUDE.md already exists:** Preserve any custom rules the user may have added below a `## Custom Rules` section. Only overwrite the CC.md-managed sections above it.

---

## Step 6 — Confirm activation

Print a confirmation summary:

```
✓ CC.md activated for {{PROJECT_NAME}}

  Memory vault: ~/.claude/projects/{{HASH}}/memory/
  ├── MEMORY.md          (index)
  ├── project_state.md   ({{PHASE}})
  ├── tech_architecture.md ({{STACK}})
  ├── learnings.md       (ready)
  └── decisions.md       (ready)

  CLAUDE.md generated in project root.

  From now on, every session in this project starts with full context.
  Memory updates happen automatically as you work.
```

---

## Proactive memory rules (active every session after activation)

These rules apply permanently once CC.md is activated. They are embedded in the generated CLAUDE.md.

1. **Session start:** Read `MEMORY.md` → load context → confirm current objective
2. **Bug fixed:** Immediately append to `learnings.md` under "Bugs & Errors"
3. **Decision made:** Immediately append to `decisions.md` as a new ADR entry
4. **Task completed:** Immediately update `project_state.md` — move to Completed, set new Next Step
5. **Pattern discovered:** Immediately append to `learnings.md` under "Success Patterns"
6. **Never defer:** Memory updates happen in the moment, not at the end of the session
