# Fab Submission Notes

This page can be used as a public-facing support document for the Fab package.

## Suggested listing description

Ink Warfare System is a multiplayer paint framework for Unreal Engine. It provides a render-target based painting pipeline, team-colored or free-color paint, paint that gameplay code can read back, late join sync, save and load, score helpers, and a playable demo with three modes: an ink battle with bots, a spray can and a water hose.

## Suggested technical summary

- C++ radial paint-hit processing with per-update budgets and view-based steps.
- Blueprint-facing interface for gameplay impacts.
- Per-impact paint material input for customizable visual results.
- Team paint, or any color or amount per hit for custom tools.
- Paint queries on floors and walls from a CPU copy of the paint.
- Paint masks sized from the mesh surface, 8 bit by default, with an unwrap cache.
- Compact hit replication, late join sync and client resync.
- Save and load of the paint of a level.
- Swim trails, drips and paint that changes over time.
- Paint tool components (spray can, water hose) that can be extended in Blueprint.
- Actor pool for bullets.
- Bots that pick zones, fight, refill and climb walls, with C++ helpers callable from Blueprint.
- `iws.Perf` performance report, stat groups and Unreal Insights scopes.

## Recommended buyer expectations

The system provides the paint framework and a demo gameplay foundation. Projects may still need custom game mode logic, UI, AI, weapon balancing, art direction, matchmaking, and production-specific optimization.

## Before shipping a project with the plugin

- Test in a packaged build.
- Test the target multiplayer mode, including a player joining late.
- Tune the radial hit budgets and mask sizes for the final maps with `iws.Perf`.
- Replace or improve the demo bots if advanced AI is required.
- Review all demo assets and third-party content licensing.
