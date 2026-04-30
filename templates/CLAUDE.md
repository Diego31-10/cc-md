# {{PROJECT_NAME}}

> CC.md active — persistent memory enabled.
> Memory vault: {{MEMORY_DIR}}

---

## Session Protocol

**At the start of every session:**
1. Read `{{MEMORY_DIR}}/MEMORY.md` — load full project context
2. Check `project_state.md` — resume from where last session left off
3. Confirm current objective before starting work

**During the session — update memory proactively:**
- Bug fixed → document immediately in `learnings.md` (problem, root cause, fix)
- Architectural decision made → record immediately in `decisions.md` (ADR format)
- Task completed → update `project_state.md` (move to Completed, set new Next Step)
- New pattern discovered → add to `learnings.md` (Success Patterns section)

**Never defer memory updates to "later"** — update in the moment.

---

## Project Context

**Stack:** {{STACK}}
**Phase:** {{PHASE}}
**Current Objective:** {{CURRENT_OBJECTIVE}}

---

## Active Constraints

{{CONSTRAINTS}}

---

## Memory Vault

| File | Purpose |
|---|---|
| `{{MEMORY_DIR}}/MEMORY.md` | Index — read first |
| `{{MEMORY_DIR}}/project_state.md` | Tasks and roadmap |
| `{{MEMORY_DIR}}/tech_architecture.md` | Stack and patterns |
| `{{MEMORY_DIR}}/learnings.md` | Bugs and success patterns |
| `{{MEMORY_DIR}}/decisions.md` | Architectural decisions (ADR) |
