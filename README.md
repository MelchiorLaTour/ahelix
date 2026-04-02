# AHelix Plugin for Claude Code

A Claude Code plugin that bundles three tools:

- **AHelix** — Pre-execution orchestrator. Scans your `.claude/` directory, scores all available tools against your task, and recommends a loadout before you start. Stops you from reaching for the wrong tool.
- **Helix Debate** — Multi-agent persuasion system. Spawns up to 18 isolated subagents across 3 layers (Believers, Analysts, Workers) to stress-test ideas and deliver a verdict. Real isolated agents, not one mind roleplaying.
- **Buddy Onboarding** — Sets up your Claude Code companion pet as a persistent second brain. Your pet speaks at the end of every response — flags risks, catches blind spots, and stays quiet when there's nothing to say.

---

## Install

1. Copy the `skills/` folder into your Claude Code config:

```bash
cp -r skills/ahelix ~/.claude/skills/ahelix
cp -r skills/helix-debate ~/.claude/skills/helix-debate
cp -r skills/buddy-onboarding ~/.claude/skills/buddy-onboarding
```

2. Restart Claude Code (or start a new session).

3. The skills are now available:
   - `/ahelix` — run the pre-execution scanner
   - `helix debate: [your topic]` — start a debate
   - `/buddy-onboarding` — set up your companion pet

---

## AHelix

Scans all agents, skills, and commands in your `.claude/` directory. Scores them against your task using a capability taxonomy + semantic overlap check. Shows a ranked loadout.

**Inventory only:**
```
/ahelix
```

**Score against a task:**
```
/ahelix stress test this architecture decision
```

**Tune it:** Edit `~/.claude/skills/ahelix/config.json`:
```json
{
  "optionsPerTask": 3,
  "semanticPenalty": 0.15
}
```

---

## Helix Debate

Runs a structured multi-agent debate with real isolated subagents. Each agent only sees what the orchestrator explicitly passes — they can't read each other's reasoning.

**Start a debate:**
```
helix debate: should we use microservices or a monolith?
```

Claude will ask clarifying questions, show you the plan (agent count, rings, token estimate), and run the debate. Each ring output is shown. A verdict is delivered.

See `skills/helix-debate/INSTALL.md` for full usage guide.

---

## Buddy Onboarding

The Claude Code companion pet (`/buddy`) already exists in Claude Code. This skill does the one setup step that makes it a persistent second brain: writes the pet's context file so it speaks in every session.

```
/buddy-onboarding
```

Follow the prompts. Your pet will speak at the end of every Claude response from that session forward.

---

## Requirements

- Claude Code (CLI or Desktop)
- The Helix Debate and AHelix skills use the `Agent` tool — they require Claude Code (not claude.ai chat)

---

## License

MIT
