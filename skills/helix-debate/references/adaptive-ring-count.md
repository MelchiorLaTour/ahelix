# Adaptive Ring Count

Ring count is no longer fixed at 2. It adapts to topic complexity.

## Classification Rule

At Phase 0, classify the topic before spawning agents:

| Complexity | Default Rings | Agent Count | Heuristics |
|---|---|---|---|
| **Simple** | 1 | 15 | Clear binary choice, <3 stakeholders, low controversy |
| **Complex** | 2 | 18 | Multi-stakeholder, technical trade-offs, moderate controversy |
| **Adversarial** | 3 | 18 | High stakes, conflicting values, political/ethical tension |

**Max rings**: 4 (hard cap). After 4 rings, escalate to user regardless.

## Heuristic Indicators

### Simple (1 ring)
- Topic can be stated as a binary question
- Fewer than 3 natural stakeholder groups
- Low emotional charge
- Likely resolved with evidence alone
- Example: "Should we use Postgres or MySQL?"

### Complex (2 rings)
- Multiple valid perspectives that don't reduce to binary
- 3-6 stakeholder groups with different priorities
- Mix of technical and human factors
- Requires trade-off analysis
- Example: "Microservices vs monolith for our 50-person team?"

### Adversarial (3+ rings)
- Deep value conflicts between stakeholders
- High stakes (financial, ethical, reputational)
- History of failed consensus attempts
- Requires multi-round persuasion to surface hidden assumptions
- Example: "Should we open-source our core product?"

## Dynamic Adjustment

Ring count is a starting estimate, not a commitment. Actual rings may be fewer
or more based on termination conditions:

- If simple topic resolves in Ring 1 → done (don't force Ring 2)
- If complex topic hits <10% flip rate after Ring 1 → resolve early
- If adversarial topic still has >30% flip rate after Ring 3 → allow Ring 4

## User Override

The user can always:
- Request additional rings ("keep going")
- Request early resolution ("resolve now")
- Switch from simple to complex mode mid-debate if complexity emerges

## Token Cost Estimates

| Complexity | Estimated Tokens | Time-to-Resolution |
|---|---|---|
| Simple (1 ring, 15 agents) | ~2,200 | ~3 min |
| Complex (2 rings, 18 agents) | ~5,400 | ~7 min |
| Adversarial (3 rings, 18 agents) | ~8,000 | ~10 min |
| Max (4 rings, 18 agents) | ~10,500 | ~13 min |

Present these estimates to the user at Phase 0 before starting.
