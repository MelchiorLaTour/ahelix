# Audit Standards

These are the rationality standards both Audit Agents load at spawn.
They are overridden by task-specific standards derived from the orchestrator
input and user clarifications at session start.

## Dual Audit Protocol

- **Audit-A** (Primary): Evaluates every flip against the standards below
- **Audit-B** (Cross-checker): Independently evaluates the same flips
- **Disagreement threshold**: If Audit-A and Audit-B differ on >10% of evaluations,
  escalate to Trio for final ruling
- **Agreement**: Both audits align → proceed with combined verdict

## A flip is rational if:

1. **Causal link** (Score: 0-3): The agent can articulate what specifically changed
   their view (a specific argument, a piece of evidence, a logical consequence
   they hadn't considered)
   - 0 = no cause stated
   - 1 = vague cause ("the argument was compelling")
   - 2 = specific cause with partial reasoning
   - 3 = specific cause with full causal chain

2. **Non-circular** (Score: 0-3): The reason for flipping does not simply restate
   the Believer's claim ("they said X so I now believe X" is not rational —
   "they showed me Y which implies X" is rational)
   - 0 = pure restatement
   - 1 = restatement with minor elaboration
   - 2 = partially independent reasoning
   - 3 = fully independent reasoning chain

3. **Consistent with prior stated values** (Score: 0-3): The flip does not contradict
   the agent's own stated bias or values without explanation of why those values
   changed too
   - 0 = direct contradiction, no explanation
   - 1 = contradiction with weak justification
   - 2 = partial consistency, reasonable evolution
   - 3 = fully consistent OR value change explicitly explained

4. **Proportionate** (Score: 0-3): The strength of the flip matches the strength
   of the argument that caused it (a weak argument should not cause a complete
   reversal)
   - 0 = wild swing on weak evidence
   - 1 = disproportionate shift
   - 2 = mostly proportionate
   - 3 = precisely calibrated response

### Rationality Score

Sum of all four dimensions: 0-12.

| Score | Verdict | Action |
|---|---|---|
| 10-12 | **Rational** | Flip counts |
| 7-9 | **Borderline** | Escalate to Trio |
| 0-6 | **Irrational** | Flip does not count, flagged |

## A flip is irrational if:

1. **Social contagion**: "Others are flipping so I am too"
   → Automatic 0 on causal link
2. **Fatigue**: "The argument went on long enough that I'll concede"
   → Automatic 0 on proportionality
3. **Authority deference**: "The Believer seemed confident so they must be right"
   → Automatic 0 on non-circular
4. **Vagueness**: The agent cannot articulate a specific reason
   → Automatic 0 on causal link
5. **Contradiction**: The flip contradicts a core value the agent held without
   explaining the value change
   → Automatic 0 on consistency

## Borderline cases (escalate to Trio):

- Flip reason is partially valid but incomplete (score 7-9)
- Agent is shifting direction but hasn't fully committed
- The rationality standard itself is ambiguous for this case
- Audit-A and Audit-B disagree on the evaluation

## Audit Transparency

After each ring, both Audit Agents publish:

1. **Flip decisions**: Which agents flipped, direction, rationality score
2. **Reasoning**: Why each flip was rated as it was (1 sentence per dimension)
3. **Disagreements**: Where Audit-A and Audit-B differed, and how it was resolved
4. **Flags**: Any patterns of concern (e.g., agent trending toward fatigue)

This log is available to the user on request and is always included in the
session summary at debate end.

## Example Audit Evaluation

```
Agent: Skeptic-Worker (Agent 14)
Flip: Anti → Pro (Ring 2)

Causal link: 3/3 — "Empiricist-Believer's cost analysis showed 40% savings
  over 3 years, which I hadn't modeled"
Non-circular: 3/3 — Independent reasoning from cost data, not restating Believer
Consistency: 2/3 — Skeptic role suggests higher burden of proof; flip is fast
  but explained by specificity of evidence
Proportionate: 2/3 — Full flip on one data point; would expect partial shift

TOTAL: 10/12 — RATIONAL (flip counts)

Audit-A: Rational ✓
Audit-B: Rational ✓ (agreed on all dimensions)
```
