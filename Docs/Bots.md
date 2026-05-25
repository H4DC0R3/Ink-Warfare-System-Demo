# Bots

The demo includes simple bots to help test the gameplay loop without requiring a full player lobby.

These bots are intentionally basic. They are designed as a demo and testing tool, not as a complete production AI system.

## What the demo bots are for

- Filling teams during local tests.
- Demonstrating match flow.
- Creating basic gameplay pressure.
- Helping users see paint, respawn, and team logic working in a playable scenario.

## What the demo bots are not

- Full competitive AI.
- Advanced tactical shooters.
- Complete navigation/combat frameworks.
- Final production-ready enemy logic.

## Recommended production approach

For a real game, treat the included bots as a starting point only. Replace or extend them with project-specific AI, behavior trees, EQS, perception, ability systems, or custom movement/combat logic.

The paint system itself is independent from the bot logic. Bots only need to call the same impact flow used by players when their weapons or traces hit paintable surfaces.
