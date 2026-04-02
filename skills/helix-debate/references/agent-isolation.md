# Agent Isolation Protocol

This is the implementation guide. It tells you exactly what to pass to the
Agent tool for each agent type in the helix-debate system.

The principle: each agent runs as an isolated subagent that genuinely cannot
see other agents' reasoning. You (the orchestrator) control what each agent
knows by controlling what you put in the Agent tool's prompt.

---

## Why Isolation Produces Better Results

When all agents share a single context, the LLM unconsciously harmonizes them.
A "Contrarian" that can see the "Pragmatist" will soften to avoid contradiction.
An "Audit Agent" that generated the arguments it's auditing will confirm its
own reasoning.

Isolation breaks this. Each subagent reasons from its assigned position without
knowing where others landed. Flips are genuine — the agent shifted because it
encountered a compelling argument, not because the shared context nudged it.

This is the difference between a brainstorm (one mind, many ideas) and a debate
(many minds, one question).

---

## Spawning Debate Agents

### Believers (3 parallel Agent calls)

Each Believer receives only its bias and the topic. No other context.

```
Agent tool call:
  description: "Believer [N] argues position"
  prompt: |
    You are a debate agent. Your role: BELIEVER.
    Your assigned perspective: [ARCHETYPE NAME]
    Your core bias: [BIAS STATEMENT]

    TOPIC: [THE DEBATE TOPIC]

    Your job: Argue your position with conviction. You believe you are right.
    You are not exploring — you are persuading. Make your strongest case.

    [If Ring 2+, include LEARNING LOOP PAYLOAD here]

    Rules:
    - Write ~150 words
    - Argue from your bias, not from "balance"
    - Do not hedge or both-sides your position
    - Do not reference other agents (you don't know they exist)

    Return ONLY your argument. Nothing else.
```

**Spawn all 3 Believers in parallel** (in a single message with 3 Agent tool
calls). Collect all 3 outputs before proceeding.

### Analysts (6 parallel Agent calls)

Each Analyst receives its bias, the topic, AND the collected Believer arguments.

```
Agent tool call:
  description: "Analyst [N] responds to believers"
  prompt: |
    You are a debate agent. Your role: ANALYST.
    Your assigned perspective: [ARCHETYPE NAME]
    Your core bias: [BIAS STATEMENT]
    Your layer role: You are an optimizer and strategist. You think structurally.
    Skepticism is part of your role — you don't flip easily.

    TOPIC: [THE DEBATE TOPIC]

    THE FOLLOWING ARGUMENTS HAVE BEEN MADE IN FAVOR OF A POSITION:
    ---
    [BELIEVER 1 OUTPUT]
    ---
    [BELIEVER 2 OUTPUT]
    ---
    [BELIEVER 3 OUTPUT]
    ---

    [If Ring 2+, include LEARNING LOOP PAYLOAD here]

    Your job: Respond to these arguments from your perspective. Challenge what's
    weak. Acknowledge what's strong. Think strategically about second-order effects.

    At the end of your response, state your FLIP STATUS:
    - HELD: You maintain your original position. [1 sentence why]
    - SHIFTED: You've moved toward the Believers' position. [1 sentence what
      specifically changed your mind — must be a concrete argument, not a feeling]

    Rules:
    - Write ~150 words (argument) + flip status
    - Respond to the actual arguments above, not hypothetical ones
    - Your skepticism should be genuine, not performed
    - If nothing in the arguments is compelling, say so plainly

    Return your response and flip status. Nothing else.
```

**Spawn all 6 Analysts in parallel.** Collect all outputs.

### Workers (6-9 parallel Agent calls)

Each Worker receives its bias, the topic, Believer arguments, AND Analyst responses.

```
Agent tool call:
  description: "Worker [N] responds to debate"
  prompt: |
    You are a debate agent. Your role: WORKER.
    Your assigned perspective: [ARCHETYPE NAME]
    Your core bias: [BIAS STATEMENT]
    Your layer role: You are part of the crowd. You react instinctively.
    You don't have to be sophisticated — be honest about what resonates.

    TOPIC: [THE DEBATE TOPIC]

    ARGUMENTS MADE (IN FAVOR):
    ---
    [ALL BELIEVER OUTPUTS]
    ---

    RESPONSES (ANALYSTS):
    ---
    [ALL ANALYST OUTPUTS — arguments only, NOT their flip statuses]
    ---

    [If Ring 2+, include LEARNING LOOP PAYLOAD here]

    Your job: React. What lands? What doesn't? What's missing?

    At the end, state your FLIP STATUS:
    - HELD: You maintain your position. [1 sentence why]
    - SHIFTED: You've moved. [1 sentence what specifically moved you]

    Rules:
    - Write ~100 words + flip status
    - Be direct, even blunt
    - You don't need to engage every argument — react to what hits you
    - Don't intellectualize. If your gut says no, say no.

    Return your response and flip status. Nothing else.
```

**Important:** Do NOT include Analyst flip statuses in the Worker prompt. Workers
should respond to the arguments, not to whether Analysts were persuaded. Including
flip statuses causes social contagion ("the Analysts flipped so I should too").

**Spawn all Workers in parallel.** Collect all outputs.

---

## Spawning Audit Agents

### Audit-A and Audit-B (2 parallel Agent calls)

Both Audits receive the same inputs. Neither sees the other's evaluation.

```
Agent tool call:
  description: "Audit-[A/B] evaluates flips"
  prompt: |
    You are an independent Audit Agent for a structured debate.
    Your job: evaluate whether each agent's flip (position change) is
    rational or irrational.

    DEBATE TOPIC: [TOPIC]

    ALL ARGUMENTS THIS RING:
    ---
    [FULL RING OUTPUT — all Believer, Analyst, and Worker arguments]
    ---

    FLIP DECLARATIONS:
    ---
    [LIST: Agent Name → HELD/SHIFTED → their stated reason]
    ---

    RATIONALITY STANDARDS:
    Score each flip on four dimensions (0-3 each):
    1. Causal link — Can the agent articulate what changed their view?
    2. Non-circular — Is the reason independent of simply restating the claim?
    3. Consistency — Does the flip align with the agent's stated values?
    4. Proportionate — Does the shift match the argument's strength?

    Total: 0-12.
    - 10-12: RATIONAL (flip counts)
    - 7-9: BORDERLINE (escalate to Trio)
    - 0-6: IRRATIONAL (flip does not count)

    IRRATIONAL PATTERNS (automatic flags):
    - Social contagion: "Others flipped so I did"
    - Fatigue: "I'll concede, we've been at this long enough"
    - Authority deference: "They seemed confident"
    - Vagueness: No specific reason given
    - Contradiction: Flip contradicts agent's own values without explanation

    For each agent that declared SHIFTED, provide:
    1. Scores on all 4 dimensions (with 1-sentence reasoning each)
    2. Total score
    3. Verdict: RATIONAL / BORDERLINE / IRRATIONAL
    4. If IRRATIONAL, which pattern was detected

    For agents that HELD, note "No flip — no audit required."

    Return ONLY the audit evaluation. No commentary, no suggestions.
```

**Spawn Audit-A and Audit-B in parallel.** Compare their evaluations.
If they disagree on >10% of verdicts, note the disagreements for the Trio.

---

## Spawning the Orchestrator Trio

The Trio runs **sequentially** (each sees the previous). This is intentional —
the Devil's Advocate must challenge the actual judgment, and the Angel must
rebut the actual challenge.

### Step 1: Orchestrator

```
Agent tool call:
  description: "Orchestrator synthesizes ring [N]"
  prompt: |
    You are the Orchestrator for a structured multi-agent debate.

    TOPIC: [TOPIC]

    RING [N] FULL OUTPUT:
    ---
    [ALL ARGUMENTS FROM THIS RING]
    ---

    AUDIT REPORTS:
    ---
    Audit-A: [AUDIT-A OUTPUT]
    Audit-B: [AUDIT-B OUTPUT]
    Disagreements: [ANY, or "None"]
    ---

    VALIDATED FLIPS THIS RING: [LIST OF RATIONAL FLIPS ONLY]
    INVALIDATED FLIPS: [LIST OF IRRATIONAL FLIPS]

    [If Ring 2+: PREVIOUS TRIO VERDICT + LEARNING LOOP]

    Your job:
    1. Synthesize what happened this ring (2-3 sentences)
    2. Make a provisional judgment: Is the debate converging? Who has
       the stronger position? What's unresolved?
    3. Issue a verdict: PROCEED (next ring) / RE-RING (repeat with
       modifications) / ESCALATE (ask user) / RESOLVE (call resolution)

    Return synthesis + judgment + verdict. Be specific, not vague.
```

### Step 2: Devil's Advocate

```
Agent tool call:
  description: "Devil's Advocate challenges orchestrator"
  prompt: |
    You are the Devil's Advocate in a debate oversight trio.

    The Orchestrator just issued this judgment:
    ---
    [ORCHESTRATOR OUTPUT FROM STEP 1]
    ---

    Your job: Find the strongest challenge to this judgment.
    - What did the Orchestrator miss or underweight?
    - What's the best counterargument to their conclusion?
    - Is the verdict premature, biased, or insufficiently justified?

    Be sharp. Not rude — sharp. Your job is to prevent bad conclusions,
    not to be agreeable.

    Return your challenge in 3-5 sentences. Nothing else.
```

### Step 3: Angel

```
Agent tool call:
  description: "Angel rebuts devil's advocate"
  prompt: |
    You are the Angel in a debate oversight trio.

    The Orchestrator issued this judgment:
    ---
    [ORCHESTRATOR OUTPUT]
    ---

    The Devil's Advocate challenged it:
    ---
    [DEVIL'S ADVOCATE OUTPUT FROM STEP 2]
    ---

    Your job: Defend the Orchestrator's reasoning against the Devil's
    challenge. But only if the reasoning holds. If the Devil found a
    real flaw, acknowledge it.

    Return your rebuttal in 3-5 sentences. State whether the original
    verdict should STAND or be MODIFIED, and why.
```

---

## Expert Agents (Agents with Skill Access)

Some agents get Expert status — they can use an installed skill or MCP tool
to gather real evidence. See `references/skill-recruitment.md` for the full
protocol, prompt templates, and assignment rules.

The key difference in isolation: Expert Agents receive an additional
`EXPERT CAPABILITY` block in their prompt telling them what tool they can use.
Their output includes an `EVIDENCE` section that the orchestrator extracts and
shares with subsequent agents.

**Evidence flow:**
1. Expert Agent produces argument + evidence
2. Orchestrator extracts the EVIDENCE block
3. Evidence is included in the argument block passed to the next strand
4. All agents see the evidence; they can challenge, build on, or dismiss it

**Audit:** Expert evidence gets additional evaluation criteria (relevance,
proportionality, verifiability — each 0-3). See `skill-recruitment.md`.

---

## Learning Loop Payload (Between Rings)

After each ring, compile this minimal summary to include in the next ring's
agent prompts:

```
CONTEXT FROM PREVIOUS RING:
- [Agent Name] shifted from [position] to [position]: "[1-line reason]"
- [Agent Name] held [position]: "unconvinced by [specific gap]"
- EXPERT EVIDENCE: [Expert Name] used [skill] and found: "[1-line finding]"
- Trio verdict: [proceed/etc] — [1 sentence summary]
- Unresolved tension: [if any]
```

Keep this under 500 tokens. Its purpose is to prevent rehashing — not to
inform agents of the full state.

---

## Parallel Execution Strategy

To minimize wall-clock time, maximize parallel Agent calls:

**Within a strand:** All agents in the same strand spawn in parallel
(all Believers at once, all Analysts at once, all Workers at once).

**Between strands:** Sequential — Analysts need Believer outputs,
Workers need Analyst outputs.

**Audit:** Both Audits spawn in parallel after all arguments are collected.

**Trio:** Sequential (Orchestrator → Devil's Advocate → Angel).

**Maximum parallelism per ring:**
- 3 Believer calls (parallel)
- 6 Analyst calls (parallel)
- 6-9 Worker calls (parallel)
- 2 Audit calls (parallel)
- 3 Trio calls (sequential)
= 4 sequential batches per ring, with up to 9 parallel calls per batch.

---

## Simulation Mode Disclaimer

If the Agent tool is not available (e.g., running in claude.ai chat), the
debate will fall back to single-context simulation. This is still useful for
exploring the topic, but the results have lower confidence because agent
independence is not genuine.

When running in simulation mode, prefix the output with:

```
⚠ SIMULATION MODE: Agents are simulated in shared context. Results are
exploratory, not independently validated. For genuine multi-agent reasoning,
run this skill in Claude Code with the Agent tool available.
```
