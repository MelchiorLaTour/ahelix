# Roadmap: AHelix Plugin

**Project:** AHelix Plugin
**Milestone:** v1.0 — Public Release
**Created:** 2026-04-02

## Phase Overview

| Phase | Name | Plans | Status | Requirements |
|-------|------|-------|--------|--------------|
| 1 | Foundation | 1 | Pending | STRUCT-01, STRUCT-02, STRUCT-03 |
| 2 | Skills & Buddy | 2 | Pending | AHELIX-01–03, HELIX-01–03, BUDDY-01–04 |
| 3 | Ship | 1 | Pending | GH-01, GH-02, GH-03 |

---

## Phase 1 — Foundation

**Goal:** Repo skeleton is in place — structure, .gitignore, README. Nothing private, nothing hardcoded.

### Plan 1.1 — Repo Structure

- Create top-level directory structure (skills/, README.md, .gitignore)
- Write .gitignore covering: .env, *.key, *.pem, SESSION_STATE.md, OS files (.DS_Store), node_modules, .planning/ entries if needed
- Write README.md explaining what the plugin is, what it includes, and how to install it

**Done when:** `ls` shows skills/, README.md, .gitignore. No sensitive file types are tracked by git.

---

## Phase 2 — Skills & Buddy

**Goal:** All three skills are in the repo, portable (no hardcoded user paths), and the buddy onboarding skill works end-to-end.

### Plan 2.1 — AHelix + Helix Debate

- Copy AHelix SKILL.md from ~/.claude/skills/ahelix/ into skills/ahelix/
- Copy default config.json into skills/ahelix/
- Copy helix-debate SKILL.md and all references/ files into skills/helix-debate/
- Copy INSTALL.md into skills/helix-debate/
- Audit both skills for any hardcoded paths (e.g. /Users/melchior/) — replace with ~/.claude/

**Done when:** Both skills present, no hardcoded user paths in any file.

### Plan 2.2 — Buddy Onboarding

- Create skills/buddy-onboarding/SKILL.md
- Skill flow:
  1. Prompt: "Run /buddy first to hatch your pet, then come back and tell me its name."
  2. User provides pet name
  3. Skill writes ~/.claude/context/[petname].md with the companion voice instructions (same pattern as vexor.md)
  4. Skill updates ~/.claude/context/INDEX.md to add `[petname]: true`
  5. Confirms: "Your pet is live. It will speak at the end of every response."

**Done when:** Buddy onboarding skill writes correct files. INDEX.md update works when file exists and when it doesn't.

---

## Phase 3 — Ship

**Goal:** Public GitHub repo is live and installable by anyone.

### Plan 3.1 — GitHub Release

- Create public repo `ahelix` on GitHub via gh CLI
- Push all plugin files
- Confirm repo is public and accessible

**Done when:** `gh repo view` shows public repo. Someone can clone it and copy skills/ into their .claude/.

---

## Progress

- [ ] Phase 1 — Foundation
- [ ] Phase 2 — Skills & Buddy
- [ ] Phase 3 — Ship

---
*Roadmap created: 2026-04-02*
