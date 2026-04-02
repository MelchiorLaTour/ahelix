# Skill Recruitment Protocol

Debate agents don't just argue from opinion — they can pull in other installed
skills and connected tools to gather real evidence, run analyses, and ground
their arguments in actual data.

This is the difference between "I think the spreadsheet approach is better"
and "I ran the numbers in the xlsx skill and here's what the data shows."

---

## How It Works

### Phase 0: Skill Discovery

At the start of every debate, before spawning any agents, the orchestrator:

1. **Scans available skills** — Check the `available_skills` list in context.
   Note every skill that's loaded (e.g., xlsx, pdf, pptx, docx, etc.)

2. **Scans available MCP tools** — Check for connected services (e.g., Gmail,
   Google Calendar, Figma, Chrome, databases, etc.)

3. **Matches to topic** — For each available skill/tool, ask: "Could this
   provide evidence, data, or grounded analysis relevant to this debate?"

4. **Builds the Toolkit Manifest** — A list of skills and tools available to
   agents, with a one-line description of what each can do for this debate.

### Example Toolkit Manifest

```
TOOLKIT MANIFEST (this debate):
- xlsx: Can build spreadsheets, run calculations, model scenarios with real numbers
- pdf: Can read uploaded PDFs for source material and evidence
- docx: Can produce formatted analysis documents
- Chrome: Can search the web for current data, articles, and evidence
- Gmail: Can search emails for relevant context the user has discussed
- Figma: Can pull design context for UI/UX debates
```

Only include skills/tools that are actually relevant. Don't list every tool
if most aren't useful for this topic.

---

## Agent Types That Use Recruited Skills

Not every agent gets tools. The system is selective about who gets what.

### Expert Agents (New Role)

Expert Agents are a special assignment given to 1-3 agents in the debate. They
are regular debate agents (Analyst or Worker) who additionally have access to
a specific skill or tool.

An Expert Agent argues from its bias AND brings grounded evidence from its
assigned skill. For example:

- **Economist-Analyst + xlsx skill** → Can model financial scenarios, build
  comparison spreadsheets, calculate ROI
- **Empiricist-Believer + Chrome** → Can search for real studies, data,
  articles to support their position
- **Affected Party-Worker + Gmail** → Can search the user's emails for
  prior discussions on the topic
- **Systems Thinker-Analyst + Figma** → Can pull actual design components
  when debating UI/architecture decisions

### Who Gets Expert Status

The orchestrator assigns Expert status based on:

1. **Archetype fit** — The Economist gets the spreadsheet, not the Humanist
2. **Topic relevance** — Only assign tools that would genuinely help this debate
3. **Balance** — Don't give all tools to one side. If a Believer gets Chrome
   for research, an Analyst should also get it

**Maximum Expert Agents per debate:** 3. More than that creates noise.
Most debates need 0-2 Expert Agents.

### Expert Agent Rules

- Expert Agents still argue from their assigned bias — the tool is evidence
  support, not a neutral oracle
- The Audit Agent evaluates Expert evidence the same way it evaluates arguments:
  is it relevant? proportionate? cherry-picked?
- Expert findings are shared with the full debate (they become part of the
  argument block that other agents see)
- Expert Agents consume more tokens. Factor this into the token estimate at
  Phase 0.

---

## Skill Recruitment in the Spawn Sequence

Updated Phase 1 spawn sequence:

```
1. Scan available skills and MCP tools
2. Build Toolkit Manifest (topic-relevant skills only)
3. Assign agent archetypes (see references/agent-archetypes.md)
4. Assign Expert status to 0-3 agents (archetype + skill match)
5. Assign opposition pairings (see references/assignment-methodology.md)
6. Lock audit standards (see references/audit-standards.md)
7. Present plan to user: "[N] agents, [M] Expert Agents with [skills]"
8. Begin Ring 1
```

---

## How Expert Agents Are Spawned

Expert Agents use the same isolation protocol as regular agents, with one
addition: their prompt includes instructions to use a specific skill.

### Expert Agent Prompt Template (Analyst example)

```
Agent tool call:
  description: "Expert Analyst [N] with [SKILL] analyzes debate"
  prompt: |
    You are a debate agent. Your role: ANALYST (EXPERT).
    Your assigned perspective: [ARCHETYPE NAME]
    Your core bias: [BIAS STATEMENT]
    Your layer role: You are an optimizer and strategist with access to
    specialized tools.

    TOPIC: [THE DEBATE TOPIC]

    EXPERT CAPABILITY: You have access to the [SKILL NAME] skill.
    [SKILL DESCRIPTION — what it can do]

    YOUR TASK:
    1. First, use your expert capability to gather evidence or run analysis
       relevant to this debate. Be specific — don't just describe what you
       could do, actually do it.
    2. Then, respond to the arguments below from your perspective, grounding
       your response in what you found.

    THE FOLLOWING ARGUMENTS HAVE BEEN MADE:
    ---
    [BELIEVER OUTPUTS]
    ---

    [If Ring 2+, include LEARNING LOOP PAYLOAD here]

    At the end of your response, state:
    - EVIDENCE: [1-2 sentences summarizing what your tool found]
    - ARGUMENT: [your ~150 word response, grounded in the evidence]
    - FLIP STATUS: HELD / SHIFTED [with reason]

    Rules:
    - Your evidence should be real and specific, not hypothetical
    - If your tool can't produce relevant evidence, say so honestly
    - Don't let the tool override your bias — interpret findings through
      your perspective
    - The Audit Agent will evaluate whether your evidence use is
      proportionate and non-cherry-picked
```

### Expert Agent Prompt Template (Believer with web search)

```
Agent tool call:
  description: "Expert Believer [N] with web search"
  prompt: |
    You are a debate agent. Your role: BELIEVER (EXPERT).
    Your assigned perspective: [ARCHETYPE NAME]
    Your core bias: [BIAS STATEMENT]

    TOPIC: [THE DEBATE TOPIC]

    EXPERT CAPABILITY: You can search the web for real data, studies,
    articles, and current information to support your argument.

    YOUR TASK:
    1. Search for 1-2 pieces of real evidence that support your position
    2. Build your argument around what you find
    3. If you can't find supporting evidence, argue from reasoning alone
       (but note that you looked and didn't find empirical support)

    Rules:
    - Write ~150 words
    - Cite your sources (URL or title)
    - Argue from your bias, not from "balance"
    - Be honest if evidence is mixed or doesn't fully support your position

    Return your argument with evidence citations. Nothing else.
```

---

## How Expert Evidence Flows Through the Debate

Expert findings don't stay siloed. They become shared evidence:

1. **Expert Agent produces output** (argument + evidence section)
2. **Orchestrator extracts the EVIDENCE block** from Expert output
3. **Evidence is included in the argument block** passed to subsequent agents

This means: if a Believer-Expert runs a web search and finds a study, the
Analysts will see that study in their input. They can challenge it, build on
it, or dismiss it — but they can't ignore it.

### Evidence in the Learning Loop

Between rings, Expert evidence gets a dedicated line in the learning payload:

```
CONTEXT FROM PREVIOUS RING:
- [Agent flips as normal]
- EXPERT EVIDENCE: [Expert Agent Name] used [skill] and found: "[1-line finding]"
- Trio verdict: [as normal]
```

This ensures Ring 2+ agents know what evidence was surfaced, without
carrying the full analysis.

---

## Audit Standards for Expert Evidence

The Dual Audit evaluates Expert Agent evidence using these additional criteria:

1. **Relevance** (0-3): Is the evidence actually relevant to the debate topic?
   - 0 = irrelevant tangent
   - 3 = directly addresses a contested point

2. **Proportionality** (0-3): Does the agent use the evidence proportionately?
   - 0 = cherry-picked to support predetermined conclusion
   - 3 = evidence presented fairly, including inconvenient findings

3. **Verifiability** (0-3): Can the evidence be checked?
   - 0 = vague claim with no source
   - 3 = specific, cited, reproducible

These scores are ADDITIONAL to the standard flip audit. An Expert Agent who
shifts based on their own evidence still gets the full 0-12 flip evaluation.

---

## When NOT to Recruit Skills

Not every debate needs Expert Agents. Skip recruitment when:

- The debate is purely philosophical/values-based (no empirical evidence helps)
- No available skills are relevant to the topic
- The user explicitly wants a fast, lightweight debate
- Token budget is tight (Expert Agents add ~200-500 tokens each)

When in doubt, ask the user: "I can give [N] agents access to [skills] to
gather real evidence. Want me to include Expert Agents, or keep it pure argument?"

---

## Skill-Specific Recruitment Patterns

### xlsx (Spreadsheet)
**Best for:** Financial debates, cost comparisons, data modeling, ROI analysis
**Assign to:** Economist-Analyst, Risk Manager-Analyst, Pragmatist-Believer
**What they do:** Build quick comparison models, calculate projections

### Chrome (Web Search)
**Best for:** Any debate where current data, studies, or market info matters
**Assign to:** Empiricist-Believer, Futurist-Analyst, Historian-Worker
**What they do:** Find real studies, articles, data to ground arguments

### pdf / docx (Document Reading)
**Best for:** Debates about uploaded documents, policies, contracts
**Assign to:** Institutionalist-Believer, Moralist-Analyst, Insider-Worker
**What they do:** Read source material and extract relevant evidence

### Figma
**Best for:** UI/UX debates, design system decisions, visual architecture
**Assign to:** Systems Thinker-Analyst, Affected Party-Worker, Outsider-Worker
**What they do:** Pull actual design context, components, screenshots

### Gmail / Calendar
**Best for:** Debates about team decisions, meeting context, prior discussions
**Assign to:** Insider-Worker, Historian-Worker, Institutionalist-Believer
**What they do:** Search for prior context the user discussed

### Custom / User Skills
**Best for:** Domain-specific debates matching the skill's domain
**Assign to:** Whatever archetype best fits the skill's domain
**What they do:** Whatever the skill does — the orchestrator reads the skill's
description and assigns accordingly
