# Ink Warfare System

**Ink Warfare System** is a multiplayer paint shooter framework for Unreal Engine. It provides a paint impact pipeline, team-based paint ownership, scoring helpers, and a playable demo project that shows the system working in a game-like setup.

This public repository contains documentation, screenshots, demo instructions, and release notes.

## Documentation

Start here:

- [Getting Started](Docs/GettingStarted.md)
- [Painting System Overview](Docs/PaintingSystemOverview.md)
- [Ink Hits Classes](Docs/InkHitsClasses.md)
- [IWS Receive Impact](Docs/IWSReceiveImpact.md)
- [Optimization and Performance](Docs/OptimizationAndPerformance.md)
- [Bots](Docs/Bots.md)
- [FAQ](Docs/FAQ.md)

## Main features

- Radial paint hits on supported static meshes.
- Team-colored paint ownership.
- Paint material override per impact.
- Render-target based impact accumulation.
- C++ optimized hit processing path.
- Blueprint-facing gameplay interface for weapons, projectiles, and gameplay actors.
- Demo setup with basic multiplayer-oriented gameplay classes and simple bots.

## What is included in this repository

- Public documentation.
- Screenshots and setup references.
- Demo download instructions.
- Basic support information.

## What is not included

- Plugin source code.
- Fab package files.
- Private development project files.
- Marketplace-only content.

## Recommended first test

1. Install the plugin from Fab.
2. Open the included or downloaded demo project.
3. Open the demo map.
4. Start Play In Editor.
5. Shoot a paintable mesh and verify that paint appears on the surface.
6. Test paint color changes by changing the team passed to the impact call.
7. Test a packaged build if the project will be used outside the editor.

## Support

Discord:
