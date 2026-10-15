# Ink Warfare System

**Current version: 2.0** · [Changelog](CHANGELOG.md)

**Ink Warfare System** is a multiplayer paint framework for Unreal Engine. It provides a paint impact pipeline, team-based paint ownership, paint that gameplay code can read back, scoring helpers, and a playable demo project that shows the system working in three game-like setups: an ink battle, a spray can and a water hose.

The demo project is included inside the plugin package, so it can be used as the first reference project after installing the plugin. This public repository contains documentation, screenshots, demo instructions, and release notes.

[![Discord Support](https://img.shields.io/badge/Discord-Support%20Contact-5865F2?logo=discord&logoColor=white)](https://discord.gg/xSj4qdgZvd)

Use the Discord link above for support contact, setup questions, bug reports, and general help with the plugin.

> **Demo project config reference**  
The demo project configuration used to produce the downloadable demo is available in `Demo/Config/DefaultEngine.ini` and `Demo/Config/DefaultEditor.ini`. These files are provided only as a visual/reference baseline, because a default Unreal Engine project uses different editor and project settings and may not match the look presented in the downloadable demo.


## Documentation

Start here:

- [Getting Started](Docs/GettingStarted.md)
- [Painting System Overview](Docs/PaintingSystemOverview.md)
- [Mesh Setup](Docs/MeshSetup.md)
- [Material Setup](Docs/MaterialSetup.md)
- [Ink Hits Classes](Docs/InkHitsClasses.md)
- [IWS Receive Impact](Docs/IWSReceiveImpact.md)
- [Paint Queries](Docs/PaintQueries.md)
- [Networking](Docs/Networking.md)
- [Paint Tools and Demos](Docs/PaintToolsAndDemos.md)
- [Trails, Splats and Drips](Docs/TrailsSplatsAndDrips.md)
- [Actor Pool](Docs/ActorPool.md)
- [Optimization and Performance](Docs/OptimizationAndPerformance.md)
- [Bots](Docs/Bots.md)
- [FAQ](Docs/FAQ.md)
- [Changelog](CHANGELOG.md)

## Main features

- Radial paint hits on supported static meshes.
- UV channel 1 based paint unwrap workflow.
- Material function based shader integration.
- Team-colored paint ownership, or free colors and amounts per hit for tools like a spray can or a water hose.
- Paint material override per impact.
- Paint queries: read which team painted a spot, the area under an actor or the share of a zone, on floors and walls.
- Paint masks sized from the mesh surface, with shared unwrap captures to keep memory low.
- Compact hit replication with late join sync and client resync.
- Save and load of the paint of a level.
- Swim trails, drips and paint that changes over time.
- Actor pool for bullets and other short-lived actors.
- Performance report on screen, in the log and in Blueprint.
- Blueprint-facing gameplay interface for weapons, projectiles, and gameplay actors.
- Demo with three modes (ink battle, spray, cleaning), multiplayer lobby and bots that paint, fight, refill and climb walls.

## What is included in this repository

- Public documentation.
- Screenshots and setup references.
- Demo project information.
- Release notes.
- Basic support information.

## What is not included

- Plugin source code.
- Fab package files.
- Private development project files.
- Marketplace-only content.

## Recommended first test

1. Install the plugin from Fab.
2. Open the demo project included inside the plugin package.
3. Open `LVL_IWS_MainMenu` and start Play In Editor.
4. Host a match and pick a map: Ink Battle, Spray or Cleaning.
5. Shoot, spray or wash a paintable mesh and verify that paint appears on the surface.
6. Test paint color changes by changing the team passed to the impact call.
7. Test a packaged build if the project will be used outside the editor.

## Support

For support contact, setup help, bug reports, and general questions, use Discord:

[![Discord Support](https://img.shields.io/badge/Discord-Support%20Contact-5865F2?logo=discord&logoColor=white)](https://discord.gg/xSj4qdgZvd)
