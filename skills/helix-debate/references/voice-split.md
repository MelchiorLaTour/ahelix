# Voice Split System

Used when a topic has fewer than 3 distinct natural opinions.
Instead of forcing artificial diversity, each opinion is split into voices
and distributed evenly across all three layers.

## How It Works

1. Identify the N distinct opinions (N < 3 typically triggers this)
2. Split each opinion into sub-voices:
   - Each voice represents the same underlying opinion but through a different lens
   - The lens is determined by the layer the voice is assigned to

## Example: 2 Opinions

Opinion A (pro) and Opinion B (anti) with 18 agents:

| Layer | Agent slots | Assignment |
|---|---|---|
| Believers (3) | 1-3 | A-Believer, B-Believer, A-Believer |
| Analysts (6) | 4-9 | A-Analyst, B-Analyst, A-Analyst, B-Analyst, A-Analyst, B-Analyst |
| Workers (9) | 10-18 | Alternating A/B Worker voices |

## Layer Role Shapes the Voice

A Worker holding Opinion A argues differently than an Analyst holding Opinion A:
- **Analyst-A**: "The data supports this, the process flow validates it, here's the optimization case"
- **Worker-A**: "This just feels right and here's why everyone I know agrees"

Same opinion. Different epistemic register. The layer role is the lens.

## Distribution Rule

Distribute as evenly as possible across layers. If uneven, give the extra voice
to the Workers layer (largest layer, most tolerance for noise).
