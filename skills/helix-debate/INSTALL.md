# Installing helix-debate in Claude Code

## Quick Install

1. In your terminal, navigate to your project (or home directory):
   ```bash
   cd ~/your-project
   ```

2. Create the skills directory if it doesn't exist:
   ```bash
   mkdir -p .claude/skills
   ```

3. Copy the `helix-debate` folder into it:
   ```bash
   cp -r /path/to/helix-debate .claude/skills/helix-debate
   ```

   Or if you have the .skill file:
   ```bash
   unzip helix-debate-v3.skill -d .claude/skills/
   ```

4. Verify the structure:
   ```
   .claude/skills/helix-debate/
   ├── SKILL.md
   └── references/
       ├── adaptive-ring-count.md
       ├── agent-archetypes.md
       ├── agent-prompts.md
       ├── assignment-methodology.md
       ├── audit-standards.md
       ├── relationship-logic.md
       ├── resolution-examples.md
       ├── termination-logic.md
       └── voice-split.md
   ```

5. Start Claude Code. The skill will automatically be available.

## How to Use It

Just describe what you want debated. Any of these will trigger the skill:

- "Helix debate: should we use microservices or monolith?"
- "Stress-test this idea: we should open-source our SDK"
- "I need all perspectives on whether to hire senior vs junior engineers"
- "Run a 3-6-9 debate on our pricing strategy"

Claude will then:
1. Ask you clarifying questions
2. Show you the plan (agent count, ring count, token estimate)
3. Ask you to pick Front or Back mode
4. Run the debate with real isolated subagents
5. Show you each ring's output
6. Deliver a verdict

## Requirements

- **Claude Code** (CLI or Desktop) — the skill uses the Agent tool for subagents
- **Cowork** also works (same Agent tool is available)
- Does NOT work in regular claude.ai chat (no Agent tool available)

## Where It Works

| Environment | Works? | Agent Isolation? |
|---|---|---|
| Claude Code CLI | Yes | Yes — real subagents |
| Claude Code Desktop | Yes | Yes — real subagents |
| Cowork | Yes | Yes — real subagents |
| claude.ai chat | No | No Agent tool |
| Claude API | Partial | You'd need to implement agent orchestration yourself |

## Tips

- **Simple topics**: Say "keep it quick" or "simple mode" to get 15 agents, 1 ring
- **Complex topics**: Default behavior, 18 agents, 2 rings
- **Go deep**: Say "adversarial mode" or "stress-test hard" for 3+ rings
- **Stop early**: Say "resolve now" at any point to get the current verdict
- **Add agents**: In Front mode, you can ask to add new perspectives mid-debate
