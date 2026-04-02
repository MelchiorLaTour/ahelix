# Requirements: AHelix Plugin

**Defined:** 2026-04-02
**Core Value:** Any Claude Code user installs once and gets AHelix, Helix Debate, and a pet companion with zero manual setup.

## v1 Requirements

### Plugin Structure

- [ ] **STRUCT-01**: Repo has valid .gitignore that excludes env files, secrets, OS files, session states
- [ ] **STRUCT-02**: README documents what the plugin does and how to install it
- [ ] **STRUCT-03**: Plugin structure is installable by copying skills/ to .claude/skills/

### AHelix

- [ ] **AHELIX-01**: AHelix SKILL.md present and complete with all steps (scan, read, score, loadout, confirm loop)
- [ ] **AHELIX-02**: Default config.json included so users can tune optionsPerTask and semanticPenalty
- [ ] **AHELIX-03**: All hardcoded user paths replaced with portable ~/.claude/ paths

### Helix Debate

- [ ] **HELIX-01**: helix-debate SKILL.md present and complete
- [ ] **HELIX-02**: All reference files present (agent-prompts, archetypes, audit-standards, etc.)
- [ ] **HELIX-03**: INSTALL.md included for standalone users

### Buddy Onboarding

- [ ] **BUDDY-01**: Skill prompts user to try /buddy to hatch their pet first
- [ ] **BUDDY-02**: After pet is named, skill writes ~/.claude/context/[petname].md with pet's voice instructions
- [ ] **BUDDY-03**: Skill updates ~/.claude/context/INDEX.md to enable the pet context
- [ ] **BUDDY-04**: Skill handles edge case where INDEX.md doesn't exist yet

### GitHub Release

- [ ] **GH-01**: Public repo created at github.com/[user]/ahelix
- [ ] **GH-02**: Initial commit pushed with all plugin files
- [ ] **GH-03**: Repo is public and installable by anyone

## v2 Requirements

### Enhancements

- **V2-01**: Plugin marketplace listing (when Claude Code marketplace exists)
- **V2-02**: Buddy personality evolution — pet context file updates based on session history
- **V2-03**: AHelix config wizard skill for first-time setup

## Out of Scope

| Feature | Reason |
|---------|--------|
| Compiled/binary components | Plugin is pure markdown — no build step |
| User-specific configuration | Public repo cannot contain personal data |
| Auto-update mechanism | Manual install for v1 |
| Web UI for configuration | Claude Code terminal is the interface |

## Traceability

| Requirement | Phase | Status |
|-------------|-------|--------|
| STRUCT-01 | Phase 1 | Pending |
| STRUCT-02 | Phase 1 | Pending |
| STRUCT-03 | Phase 1 | Pending |
| AHELIX-01 | Phase 2 | Pending |
| AHELIX-02 | Phase 2 | Pending |
| AHELIX-03 | Phase 2 | Pending |
| HELIX-01 | Phase 2 | Pending |
| HELIX-02 | Phase 2 | Pending |
| HELIX-03 | Phase 2 | Pending |
| BUDDY-01 | Phase 2 | Pending |
| BUDDY-02 | Phase 2 | Pending |
| BUDDY-03 | Phase 2 | Pending |
| BUDDY-04 | Phase 2 | Pending |
| GH-01 | Phase 3 | Pending |
| GH-02 | Phase 3 | Pending |
| GH-03 | Phase 3 | Pending |

**Coverage:**
- v1 requirements: 16 total
- Mapped to phases: 16
- Unmapped: 0 ✓

---
*Requirements defined: 2026-04-02*
*Last updated: 2026-04-02 after initial definition*
