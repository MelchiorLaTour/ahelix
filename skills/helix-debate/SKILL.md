---
name: helix-debate
description: >
  Use this skill whenever a user wants to deeply analyze, stress-test, or reach
  a resolution on a complex topic using a structured multi-agent persuasion system.
  Triggers include: "helix debate", "multi-agent analysis", "argue all sides",
  "3 6 9 debate", "stress-test this idea", "get all perspectives", "build a debate
  system", or any request for structured multi-perspective deliberation with a
  resolution goal. Also triggers when a user wants to validate a plan, challenge
  an assumption, or reach a defensible conclusion across competing viewpoints.
  The system uses up to 18 biased agents across three layers (Believers, Analysts,
  Workers), a Dual Orchestrator with Devil's Advocate and Angel, dual Audit Agents,
  and a learning loop across rings. Resolution threshold depends on mode: all 6
  Analysts (back-end) or all 9 Workers (front-end). IMPORTANT: This skill uses
  the Agent tool to spawn each debate agent as an isolated subagent — each agent
  reasons independently with no access to other agents' internal thinking.
---

# Helix Debate Skill

A full multi-agent persuasion and resolution system. Not a debate for insight —
a debate with a **verdict**.

**What makes this different from a simulated debate**: Every agent runs as a
separate subagent call via the Agent tool. Each agent has its own isolated context
and can only see what the orchestrator explicitly passes to it. This means agents
genuinely cannot read each other's reasoning — they only see the final arguments
from prior strands, just like real participants in a structured debate.

---

## How It Actually Works (The Agent Tool Pattern)

YOU (the main Claude session) are the Orchestrator. You do not simulate agents
by writing their dialogue. Instead, you spawn each agent as a subagent using the
Agent tool, collect their output, and route it to the next strand.

The flow looks like this:

```
YOU (Main Session = Back Orchestrator)
  │
  │  Phase 0: Talk to user, classify topic, plan the debate
  │
  │  Phase 1: Spawn agents as isolated subagent calls
  │
  │  For each ring:
  │    ├── Spawn 3 Believer agents (parallel, isolated)
  │    │   └── Each receives: bias assignment + topic + learning loop
  │    │   └── Each returns: their argument (~150 words)
  │    │
  │    ├── Collect Believer outputs
  │    │
  │    ├── Spawn 6 Analyst agents (parallel, isolated)
  │    │   └── Each receives: bias + topic + Believer arguments + learning loop
  │    │   └── Each returns: their response + flip status
  │    │
  │    ├── Collect Analyst outputs
  │    │
  │    ├── Spawn 6-9 Worker agents (parallel, isolated)
  │    │   └── Each receives: bias + topic + Believer args + Analyst args + learning loop
  │    │   └── Each returns: their response + flip status
  │    │
  │    ├── Collect Worker outputs
  │    │
  │    ├── Spawn Audit-A agent (isolated)
  │    │   └── Receives: all arguments + all flips (NOT internal reasoning)
  │    │   └── Returns: rationality scores per flip
  │    │
  │    ├── Spawn Audit-B agent (isolated)
  │    │   └── Receives: same inputs as Audit-A (independently)
  │    │   └── Returns: independent rationality scores
  │    │
  │    ├── YOU compare Audit-A and Audit-B results
  │    │
  │    ├── Spawn Devil's Advocate agent (isolated)
  │    │   └── Receives: your provisional judgment + ring summary
  │    │   └── Returns: challenge to your judgment
  │    │
  │    ├── Spawn Angel agent (isolated)
  │    │   └── Receives: DA's challenge + your judgment
  │    │   └── Returns: rebuttal defending the reasoning
  │    │
  │    ├── YOU make Trio verdict (proceed / re-ring / resolve / escalate)
  │    │
  │    └── Prepare learning loop payload for next ring
  │
  │  Resolution: Deliver verdict to user
```

### Critical Rule: What Agents Can and Cannot See

This is the entire point. If you break isolation, you're back to one mind
wearing 18 hats.

| Agent Type | CAN See | CANNOT See |
|---|---|---|
| Believer | Topic, their bias, learning loop from prior ring | Other Believers' arguments, any Analyst/Worker output |
| Analyst | Topic, their bias, ALL Believer arguments, learning loop | Other Analysts' arguments, any Worker output |
| Worker | Topic, their bias, ALL Believer + Analyst arguments, learning loop | Other Workers' arguments |
| Audit-A | All final arguments, all flip declarations | How arguments were generated, Audit-B's evaluation |
| Audit-B | All final arguments, all flip declarations | How arguments were generated, Audit-A's evaluation |
| Devil's Advocate | Orchestrator's judgment, ring summary | Individual agent reasoning, audit scores |
| Angel | DA's challenge, Orchestrator's judgment | Individual agent reasoning, audit scores |

**Within a strand, agents run in parallel and cannot see each other.** The three
Believers spawn at the same time and return independently. Same for Analysts
and Workers.

---

## Phase 0 — User Interface (Before Any Agents Spawn)

Before spawning anything, you (the Orchestrator) do this directly:

1. **Read the topic**: Understand what the user wants debated
2. **Classify complexity**: Simple / Complex / Adversarial
   (see `references/adaptive-ring-count.md`)
3. **Challenge the user**: Ask 2-3 pointed clarifying questions — genuine
   challenges to assumptions, not soft prompts
4. **Present the plan**: Show agent count, estimated rings, token cost, and
   Front vs Back mode options
5. **Get user confirmation** before spawning anything
6. **Disclose known limitations**: Before any agents spawn, tell the user:
   > "Note: all agents in this debate run on the same underlying model (Claude).
   > They cannot provide epistemically independent critique — they share training
   > weights and will produce correlated outputs in ways neither you nor I can
   > fully detect. Treat the verdict as structured brainstorming that surfaces
   > argument coverage, not as independent validation."
   This is not a disclaimer to hide — it's the honest framing for what the skill
   actually produces. A user who understands this interprets outputs correctly.

**Scope check**: If topic has fewer than 3 distinct natural opinions, use the
Voice Split System (see `references/voice-split.md`).

**Complexity classification**:

| Complexity | Agents | Default Rings | Token Estimate |
|---|---|---|---|
| Simple | 15 (3B + 6A + 6W) | 1 | ~2,200 |
| Complex | 18 (3B + 6A + 9W) | 2 | ~5,400 |
| Adversarial | 18 (3B + 6A + 9W) | 3 | ~8,000 |

---

## Phase 1 — Agent Spawning (How to Use the Agent Tool)

Each agent is spawned using the Agent tool with a carefully constructed prompt.
The prompt templates are in `references/agent-prompts.md`. Read that file before
spawning any agents.

### Spawn Order

```
1. Classify topic, assign biases (see references/agent-archetypes.md)
2. Assign opposition pairings (see references/assignment-methodology.md)
3. Begin Ring 1:
   a. Spawn 3 Believers in PARALLEL (one Agent call each)
   b. Collect outputs → these become input for Analysts
   c. Spawn 6 Analysts in PARALLEL (one Agent call each)
   d. Collect outputs → these become input for Workers
   e. Spawn 6-9 Workers in PARALLEL (one Agent call each)
   f. Collect outputs → proceed to Audit
4. Spawn Audit-A (one Agent call)
5. Spawn Audit-B (one Agent call, same inputs, independent)
6. Compare audit results — flag disagreements
7. Spawn Devil's Advocate (one Agent call)
8. Spawn Angel (one Agent call)
9. YOU make Trio verdict
10. If proceeding: prepare learning loop, go to step 3 for next ring
11. If resolving: deliver verdict to user
```

### Parallelism

Spawn agents within the same strand in **parallel** (multiple Agent calls in one
message). This is critical for performance — 9 Workers sequentially would be
painfully slow.

```
# Good: spawn all 3 Believers in one message with 3 Agent calls
Agent(prompt=believer_1_prompt)  ← these run simultaneously
Agent(prompt=believer_2_prompt)
Agent(prompt=believer_3_prompt)

# Bad: spawn them one at a time
Agent(prompt=believer_1_prompt)  ← wait for result
Agent(prompt=believer_2_prompt)  ← wait for result
Agent(prompt=believer_3_prompt)  ← wait for result
```

---

## The 18 Debate Agents

| Layer | Count | Role | Resolution Weight |
|---|---|---|---|
| **Believers** | 3 | Moderators. Think they're right. Argue to convince. | Perseverance advantage on draws |
| **Analysts** | 6 | Optimizers, strategists, error escalators. Skeptical by role. | **Back-end resolution gate** |
| **Workers** | 6 or 9 | Crowd. Loud. Instinctive. Don't know or disagree. | **Front-end resolution gate** |

Each agent gets (via their prompt):
- A **named archetype** (from `references/agent-archetypes.md`)
- A **core bias statement** (one sentence: what they believe and why)
- A **layer role** shaping how they express that bias
- A **helix position** (determines who they challenge — see `references/relationship-logic.md`)
- The **arguments they're allowed to see** (strand outputs from prior layers)
- The **learning loop** (if Ring 2+)

---

## The Helix Pattern

Each ring: **Believers → Analysts → Workers** (3 → 6 → 9)

Argument flow is strictly one-directional within a ring:
- Believers see nothing (they state position)
- Analysts see Believer arguments
- Workers see Believer + Analyst arguments

Between rings, the learning loop carries forward — but **only argument substance, not social proof signals**:

**What gets passed to agents in the next ring:**
- Numbered list of distinct argument claims from this ring (unattributed — no agent names, no flip counts, no direction). **Randomize the order of claims algorithmically before passing** — do not preserve the order in which they appeared in the ring. Ordering encodes a "this came first / was most prominent" signal that agents read as convergence. Randomization removes the positional map without removing the claims.
- Trio's **binary evaluation** for each distinct claim: PASS (logically sound, causal chain present) or FAIL (unsupported, circular, or unfalsifiable). No evaluative language. No strength rankings. No framing about which claims were "most persuasive" or "strongest." The goal is filtering junk — not telegraphing direction.
- One unresolved challenge question the Trio flagged

**What gets stripped entirely:**
- Agent identifiers on any claim
- Flip counts ("3 agents moved toward X")
- Flip direction signals
- Any framing that indicates momentum or majority position

**What the Orchestrator keeps privately (not passed to agents):**
- Full flip log: agent name, ring, direction, reason, audit score
- This is the accountability record — it stays with the Orchestrator only

**Why:** Popularity and quality are orthogonal. The current format conflates them
and creates herding. This was demonstrated twice in live operation: Ring 1
(adaptive ring sizing cascade) and Ring 2 (outcome tracking cascade). Stripping
identity/counts from the payload eliminates the social proof signal while
preserving all argument substance.

---

## The Dual Audit

Two independent Audit Agents, each spawned as a separate subagent.

**Audit-A prompt** receives: all arguments from the ring + all flip declarations.
It does NOT receive Audit-B's output. It scores each flip on the 0-12 rationality
scale (see `references/audit-standards.md`).

**Audit-B prompt** receives: identical inputs. It scores independently.

**You (Orchestrator) compare results:**
- If Audit-A and Audit-B agree (within 2 points per flip): accept their verdict
- If they disagree by >2 points on any flip: that flip is escalated to Trio
- Log all disagreements

---

## The Orchestrator Trio

This is where YOU (the main session) step in directly alongside two subagents:

- **You (Orchestrator)**: Synthesize the ring, form a provisional judgment
- **Devil's Advocate** (subagent): Receives your judgment + ring summary.
  Returns a sharp challenge. Its job is to find the weakest point in your
  reasoning and attack it.
- **Angel** (subagent): Receives the DA's challenge + your judgment.
  Returns a defense. Its job is to rebut the DA or acknowledge valid points.

After receiving both, YOU make the Trio verdict:
- **Proceed**: Next ring
- **Re-ring**: Same agents re-argue (rare — use when audit flagged issues)
- **Resolve**: Threshold met, deliver verdict
- **Escalate**: Ask user for input (when blocked or at max rings)

---

## Termination Conditions

Any of these ends the debate:

1. **Flip rate < 10%** in last ring (consensus stable)
2. **Rational consensus > 70%** among eligible agents (audit-validated)
3. **Max 4 rings** (timeout — escalate to user)
4. **User requests "resolve now"**

See `references/termination-logic.md` for the decision tree.

---

## Resolution Thresholds

**Back Mode**: All 6 Analysts flipped + audit-validated = resolution
**Front Mode**: All Workers (6 or 9) flipped + audit-validated = resolution
**Draw**: Believers hold perseverance advantage → Trio deliberation → if still
split, escalate to user with full per-agent breakdown

---

## Output Mode — Verdict Placement

Two modes. Choose at Phase 0 based on use case:

**Reasoning-First (default for high-stakes decisions)**
- Ring detail shown first, verdict at the end
- Reader forms judgment before encountering conclusion
- Prevents anchoring — conclusion is earned, not anchored
- Use for: strategic decisions, policy choices, anything where independent evaluation matters

**Verdict-First (for operational speed)**
- Conclusion shown immediately, ring detail follows on demand
- Prevents invisibility failure — busy readers see the verdict even if they skip the detail
- Anchoring risk is real but manageable; invisibility failure is worse in fast-paced contexts
- Use for: quick tactical decisions, time-pressured operational calls

Ask the user at Phase 0: "High-stakes (reasoning-first) or operational speed (verdict-first)?"
Default to reasoning-first if they don't specify.

---

## Resolution Format

When resolved:

```
✓ RESOLVED: [2-3 sentences: conclusion + key reasoning]
Dissents: [bullet points for significant minority views, if any]
Proceed / Adjust / Abort?
```

When blocked:

```
⚠ BLOCKED: [exact question in one sentence]
Your call.
```

See `references/resolution-examples.md` for examples at different complexity levels.

---

## Output Format Per Ring

Display this to the user after each ring completes:

```
=== RING [N] ===

[BELIEVERS — 3 agents, ran in parallel, isolated]
Agent 1 (Archetype): [their argument, as returned by subagent]
Agent 2 (Archetype): [their argument]
Agent 3 (Archetype): [their argument]

[ANALYSTS — 6 agents, ran in parallel, isolated]
Agent 4 (Archetype): [response + flip status]
Agent 5 (Archetype): [response + flip status]
... (all 6)

[WORKERS — 6-9 agents, ran in parallel, isolated]
Agent 10 (Archetype): [response + flip status]
... (all workers)

--- DUAL AUDIT REPORT ---
Audit-A scores: [Agent] → [score/12] → [rational / borderline / irrational]
Audit-B scores: [Agent] → [score/12] → [rational / borderline / irrational]
Disagreements: [if any, with resolution]

--- TRIO DELIBERATION ---
Orchestrator (you): [provisional judgment]
Devil's Advocate: [challenge, from subagent]
Angel: [rebuttal, from subagent]
Trio Verdict: [proceed / re-ring / resolve / escalate]

--- LEARNING LOOP (carried to next ring) ---
Claims (randomized order, unattributed): [numbered list of distinct argument claims, no agent names, order shuffled]
Merit evaluation (binary): [each claim: PASS or FAIL — no evaluative language]
Unresolved: [one challenge question]
[Orchestrator private log — NOT shown to agents: flip names/directions/scores]

=== END RING [N] ===
```

---

## Ring Gate Checklist

Before proceeding to the next ring, confirm ALL:

- [ ] **Context checkpoint**: Check remaining context before spawning. If below 35% remaining, compress immediately — do not start a new ring into a depleted window. If below 25%, stop and tell the user.
- [ ] All strand agents returned outputs
- [ ] **Parallel batch check**: If any parallel spawns failed or were cancelled, complete the successful calls first, then retry failed ones independently before proceeding
- [ ] Both Audit Agents scored all flips
- [ ] Audit disagreements resolved (by you or escalated)
- [ ] Devil's Advocate and Angel both returned
- [ ] Trio verdict issued
- [ ] Learning loop payload assembled (claims randomized, binary eval applied)
- [ ] Termination conditions checked (proceed or resolve?)

---

## Learned Agent Assignment (Ring 3+)

After 2 rings, you have data on which agents reasoned well:

- Track rationality scores from Audit across Rings 1-2
- In Ring 3+, you can adjust: give higher-scoring agents more detailed prompts
  or deprioritize agents who were flagged as irrational
- New agents can be introduced if coverage gaps emerge
- This is optimization, not punishment — document the reason for any changes

---

## Agent Removal Protocol

If an agent returns output that is:
- Inconsistent with their assigned bias
- Vague or unauditable
- Incoherent or off-topic

**Process:**
1. Both Audit Agents confirm the problem is pattern-based (not a one-off)
2. You (Orchestrator) decide: remove, warn, or re-prompt
3. If removed, inform the user:

```
⚠ AGENT REMOVED: [Agent N — Archetype]
Reason: [one sentence]
Replace / Proceed with N-1 / Your call.
```

4. Replacement agent spawns with a prompt informed by the removal reason
5. New agent must pass entry audit (both Audit-A and Audit-B)

---

## Front Orchestrator Mode (User-Facing Option)

When the user selects Front mode, you additionally:
- Surface the option to spawn more agents when coverage gaps are detected
- Can spawn new agents mid-debate on user request
- Present token cost estimates before each ring
- Workers are the resolution gate (all must flip for resolution)

Always offer Front mode as an option at Phase 0.

---

## Outcome Tracking — Pilot (Unvalidated)

**Status: Pilot only. Do NOT use to drive ring sizing yet.**

After delivering a verdict, optionally offer the user a 30-day retrospective:

```
Want to help calibrate this skill? In 30 days I can ask you 3 quick questions
about how this verdict held up. Takes 5 minutes. Your call.
```

If yes, log to `~/.claude/helix-debate-outcomes.jsonl`:
```json
{"verdict_id": "[topic-slug-timestamp]", "verdict": "[2-sentence summary]",
 "config": {"rings": N, "agents": N, "mode": "front|back"},
 "followup_due": "[ISO date +30 days]", "status": "pending"}
```

At followup, ask:
1. Did this outcome confirm, refute, or remain unclear?
2. Was the verdict directionally useful?
3. One sentence on what actually happened.

Update the record with responses. Aggregate by config over time.

**Why this is a pilot:** Self-reported retrospectives carry systematic bias
(social desirability, anchoring to memory). The first round of data should be
audited for distortion patterns before it informs any architectural changes.
Do not feed into ring sizing until data quality has been adversarially reviewed.

---

## Known Open Questions (Future Debates)

**Architectural Separation (not yet implemented — needs dedicated debate):**
A4 proposed the deepest structural change in the meta-debate: generative and
synthesis layers must not share context. Agents should receive only unresolved
claims between rings, never prior verdicts. This was the strongest structural
insight in 3 rings of debate and received zero adversarial pressure. It needs
its own helix debate before implementation.

**Adaptive Ring Sizing (held — depends on outcome tracking validation):**
Scale agent counts down when convergence signals emerge mid-ring. Do not
implement until outcome tracking pilot has been validated and adversarially
reviewed. The convergence detection problem (herding vs. genuine consensus)
requires external calibration to be trustworthy.

**Governance Reform — Independent Arbiter (held — needs dedicated debate):**
B3 in the self-audit proposed removing final Trio verdict authority from the
Orchestrator and assigning it to a structurally separate Arbiter agent with
no access to the Orchestrator's reasoning chain. The argument is directionally
valid (Orchestrator-as-judge is a self-check), but analysts correctly identified
that an Arbiter running on the same model weights receiving an Orchestrator-
assembled payload does not achieve epistemic independence. Do not implement
until payload isolation is solved and a dedicated debate evaluates the
governance architecture.

**Shared-Weights Convergence — Structural Limit (not fixable by skill changes):**
All agents in this skill run on the same underlying model. They share training
weights and produce correlated outputs in ways that cannot be fully detected or
eliminated through payload interventions. Claim-order randomization and binary
scoring reduce observable herding signals but cannot address the underlying
correlation. This is a fundamental property of same-model multi-agent systems.
The skill's verdict should be interpreted as structured brainstorming, not
independent validation. See Phase 0 limitations disclosure.

---

## Reference Files

Read these as needed — don't load them all upfront:

- `references/agent-prompts.md` — **Read this first.** Exact prompt templates for
  every agent type. This is what you pass to the Agent tool.
- `references/agent-archetypes.md` — Bias templates for agent assignment
- `references/assignment-methodology.md` — Opposition pairings and rotation rules
- `references/audit-standards.md` — Rationality scoring (0-12 scale)
- `references/relationship-logic.md` — 3-6-9 dynamics and challenge targets
- `references/voice-split.md` — For topics with <3 natural opinions
- `references/termination-logic.md` — When debates end
- `references/adaptive-ring-count.md` — Ring count by complexity
- `references/resolution-examples.md` — Example resolution outputs
