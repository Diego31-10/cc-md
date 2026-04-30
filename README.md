# CC.md

> **Local Memory, Global Intelligence.**

CC.md is a Claude Code plugin that combines the development workflows of superpowers with a persistent memory layer for every project. Install once, activate per project, and Claude Code becomes a project-aware senior engineer that never loses context.

---

## What it does

- **Persistent memory** — Every project gets a memory vault: stack, decisions, learnings, current state
- **Proactive updates** — Claude updates memory during the session, not at the end
- **Full superpowers** — All 14 superpowers skills included: TDD, debugging, planning, parallel agents, and more
- **One command** — `/cc-md` activates everything in under 30 seconds

---

## Installation

### Via Claude Code marketplace
```
Claude Code → Plugins → Search "cc-md" → Install
```

### Via npm
```bash
npm install -g @diegos31/cc-md
```

---

## Usage

```bash
cd your-project
claude          # open Claude Code
/cc-md          # activate memory for this project
```

That's it. From the next session onwards, Claude starts every conversation with full project context.

---

## Memory Vault

Each activated project gets these files in `~/.claude/projects/<hash>/memory/`:

| File | Purpose |
|---|---|
| `MEMORY.md` | Index — Claude reads this first every session |
| `project_state.md` | Current tasks, roadmap, active focus |
| `tech_architecture.md` | Stack, dependencies, patterns |
| `learnings.md` | Bugs fixed and success patterns |
| `decisions.md` | Architectural Decision Records (ADR) |

---

## Included Skills (from superpowers)

| Skill | Trigger |
|---|---|
| brainstorming | Before any creative work |
| systematic-debugging | Any bug or test failure |
| writing-plans | Multi-step implementation tasks |
| test-driven-development | Any feature or bugfix |
| subagent-driven-development | Complex parallel tasks |
| dispatching-parallel-agents | 2+ independent tasks |
| executing-plans | Running an existing plan |
| verification-before-completion | Before claiming work is done |
| requesting-code-review | After completing features |
| receiving-code-review | When getting review feedback |
| finishing-a-development-branch | Before merging |
| using-git-worktrees | Feature isolation |
| writing-skills | Creating new skills |
| using-superpowers | Session bootstrap |

---

**CC.md** — *Built by developers, for developers, living in Markdown.*
