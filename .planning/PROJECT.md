# AHelix Plugin

## What This Is

A public Claude Code plugin that bundles three tools: AHelix (pre-execution tool orchestrator), Helix Debate (multi-agent persuasion and resolution system), and a Buddy onboarding skill that automates setting up the Claude Code companion pet for any user who installs the plugin.

## Core Value

Any Claude Code user can install this plugin and immediately get smarter tool selection, structured multi-agent debate, and a persistent pet companion — with zero manual setup.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] AHelix skill packaged and installable
- [ ] Helix Debate skill packaged with all reference files
- [ ] Buddy onboarding skill writes context file + updates INDEX.md automatically
- [ ] .gitignore excludes all private/sensitive file types
- [ ] README documents install steps clearly
- [ ] Plugin published as public GitHub repo

### Out of Scope

- Personal configuration files — this is a public repo, no private data
- Platform-specific binaries — pure markdown/JSON, no compiled code
- Auto-publishing to plugin marketplace — manual GitHub release for v1

## Context

- AHelix and Helix Debate already exist as local skills at `~/.claude/skills/`
- The Claude Code companion pet system (/buddy) already exists in Claude Code — no build needed
- The "buddy onboarding" skill only needs to write `~/.claude/context/[petname].md` and update `~/.claude/context/INDEX.md` — exactly what was done manually for Vexor
- Plugin consumers install by copying skills into their `.claude/skills/` directory
- Must work for anyone: the context file paths must use `~/.claude/` not hardcoded user paths

## Constraints

- **Public repo**: No personal data, API keys, session states, or user-specific config
- **Pure markdown**: No runtime dependencies — skills are instructions, not compiled code
- **Portable paths**: All file paths in skills use `~/.claude/` (not `/Users/melchior/`)

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Bundle all 3 features in one plugin | Single install, coherent package | — Pending |
| Buddy onboarding writes context file directly | Mirrors what user did manually for Vexor | — Pending |
| .planning/ committed to git | User chose this; acceptable for a tools plugin | — Pending |

---
*Last updated: 2026-04-02 after initial project setup*
