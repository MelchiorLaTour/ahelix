# Resolution Examples

The Resolution Agent's output should match topic complexity. These examples
show the expected format at different lengths.

## Simple Topic — 1-2 Sentences

**Topic**: "Should we use TypeScript or JavaScript for this project?"

```
✓ RESOLVED: TypeScript — the type safety benefits outweigh the setup overhead
for a team of 8+ with a 2-year maintenance horizon. Workers and Analysts
converged on long-term maintainability as the deciding factor.
Proceed / Adjust / Abort?
```

## Standard Topic — 2-3 Sentences

**Topic**: "Microservices vs monolith for a 50-person engineering org?"

```
✓ RESOLVED: Start monolith, extract services at pain points. The Analysts
converged on premature microservices as the higher-risk path — coordination
costs dominate at 50 engineers. Workers validated that team autonomy concerns
are real but better addressed by module boundaries than service boundaries.
Dissents: Futurist-Analyst argues extraction debt compounds; Risk Manager
flags monolith scaling ceiling at ~100 engineers.
Proceed / Adjust / Abort?
```

## Complex Topic — Up to 5 Sentences + Dissent Bullets

**Topic**: "Should we open-source our core product?"

```
✓ RESOLVED: Open-source the SDK and developer tools; keep the core inference
engine proprietary for 18 months with a published roadmap to selective
open-sourcing. The Believers' case for community-driven innovation was strong
but Analysts identified revenue protection as non-negotiable in the current
funding cycle. Workers validated that developer trust requires visible
open-source commitment, not just promises. The 18-month timeline gives the
business model time to mature while building community goodwill.

Dissents:
- Disruptor-Worker: 18 months is too long; competitors will fill the gap
- Moralist-Analyst: Selective open-sourcing creates trust asymmetry
- Economist-Analyst: Revenue protection argument assumes current pricing holds

Proceed / Adjust / Abort?
```

## Escalation — Blocked

**Topic**: "Should we replace our CEO?"

```
⚠ BLOCKED: The debate cannot resolve without clarity on whether the board
has already initiated succession planning — this changes the feasibility
calculus for all agents.
Your call.
```

## Escalation — Timeout (Max Rings)

**Topic**: "Universal basic income: implement or not?"

```
⚠ TIMEOUT (4 rings): No consensus reached. Current state:
- 11/18 agents favor conditional UBI with means testing
- 5/18 agents favor unconditional UBI
- 2/18 agents oppose any UBI implementation
Key tension: Empiricist and Economist disagree on inflation impact modeling.
Continue (Ring 5) / Accept majority position / Your call.
```

## Format Rules (Reminder)

- No preamble before the ✓ or ⚠ symbol
- No context-setting ("After careful deliberation...")
- No hedging ("It seems like..." / "On balance...")
- State the conclusion, then the evidence, then dissents
- Let the user decide next steps
