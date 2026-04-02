# Termination Logic

When does a debate end? Not by default ring count — by evidence of convergence
or exhaustion.

## Decision Tree

```
START → Classify topic complexity
  │
  ├─ Simple → Default 1 ring
  │    └─ After Ring 1: flip rate < 10%? → RESOLVE
  │                      flip rate ≥ 10%? → Ring 2 (max)
  │
  ├─ Complex → Default 2 rings
  │    └─ After each ring:
  │         flip rate < 10%? → RESOLVE
  │         rational consensus > 70%? → RESOLVE
  │         Ring 4 reached? → TIMEOUT → escalate to user
  │         Otherwise → next ring
  │
  └─ Adversarial → Default 3 rings
       └─ Same checks as Complex, but expect 3+ rings
          Max 4 rings hard cap regardless
```

## Termination Conditions (any one triggers resolution)

1. **Flip rate < 10%**: Fewer than 10% of eligible agents changed position in the
   last ring. Consensus is stable — further rings won't move the needle.

2. **Rational consensus > 70%**: More than 70% of eligible agents hold the same
   position AND their reasoning has been audit-validated as rational. Strong
   evidence-based convergence.

3. **Max 4 rings**: Hard timeout. After 4 rings, escalate to user with:
   - Current position breakdown (per agent)
   - Unresolved tensions
   - Trio's best recommendation
   - Option to continue (user override) or accept current state

4. **User override**: User requests "resolve now" at any point. Resolution Agent
   delivers current state immediately.

## What Happens at Termination

1. Resolution Agent fires with appropriate format (see SKILL.md)
2. Learning loop data archived (available for future reference)
3. Dual Audit publishes final report
4. Session summary generated for user

## Anti-Patterns

- **Running rings "just because"**: If flip rate is 0% after Ring 1, do NOT
  run Ring 2. The debate is over.
- **Ignoring user fatigue**: If the user seems disengaged, offer early resolution.
- **Gaming termination**: Agents should not strategically withhold flips to
  extend the debate. Audit catches this pattern.

## Examples

**Simple topic** (e.g., "Should we use TypeScript or JavaScript for this project?"):
→ 1 ring, 15 agents, resolves in ~2,200 tokens

**Complex topic** (e.g., "What's the optimal microservices vs monolith architecture?"):
→ 2 rings, 18 agents, resolves in ~5,400 tokens

**Adversarial topic** (e.g., "Should we open-source our core IP?"):
→ 3 rings, 18 agents, resolves in ~8,000 tokens, may escalate to user
