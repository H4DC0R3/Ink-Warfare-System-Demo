# Fab Submission Notes

This page can be used as a public-facing support document for the Fab package.

## Suggested listing description

Ink Warfare System is a multiplayer-oriented paint shooter framework for Unreal Engine. It provides a render-target based painting pipeline, team-colored paint ownership, radial paint impacts, score helpers, demo classes, and a playable sample setup for testing paint-based gameplay.

## Suggested technical summary

- C++ optimized radial paint-hit processing.
- Blueprint-facing interface for gameplay impacts.
- Per-impact paint material input for customizable visual results.
- Team-based paint ownership.
- Render-target based paint accumulation.
- Demo Blueprint setup using `BP_IWS_PS_InkHits`.
- Legacy Blueprint references included for learning and compatibility.
- Simple demo bots for local gameplay testing.

## Recommended buyer expectations

The system provides the paint framework and demo gameplay foundation. Projects may still need custom game mode logic, UI, AI, weapon balancing, art direction, matchmaking, and production-specific optimization.

## Before shipping a project with the plugin

- Test in a packaged build.
- Test the target multiplayer mode.
- Tune radial hit update budgets for the final maps.
- Replace or improve demo bots if advanced AI is required.
- Review all demo assets and third-party content licensing.
