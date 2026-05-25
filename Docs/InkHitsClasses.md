# Ink Hits Classes

This page describes the main classes used by the paint hit system.

## `IWS_Ink_Hits`

`IWS_Ink_Hits` is the main C++ class for the optimized paint pipeline.

It does not update the whole match by itself. Instead, it exposes the core functions and utilities needed by other classes to register impacts, update radial hits, manage paintable meshes, and draw paint into render targets.

Main responsibilities:

- Receive paint impacts through the gameplay interface.
- Replicate paint hit registration through server and multicast calls.
- Register static mesh components as paintable entries when they receive hits.
- Store active hit data per paintable mesh.
- Manage render targets for impact and unwrap textures.
- Capture surface unwrap data when required.
- Draw hit materials into the impact render target.
- Process radial hit animation over time.
- Remove expired hits.
- Apply performance budgets for captures, paintables, and draw operations.

## `BP_IWS_PS_InkHits`

`BP_IWS_PS_InkHits` is the class used by the demo project. It is derived from `BP_IWS_ZoneCheck`.

This class contains the demo loop where the system registers paintable assets and drives the main update flow used in the demo. For most users, this Blueprint is the best practical reference for how the paint system is expected to be placed and configured in a playable project.

## Legacy Blueprint classes

The system also includes legacy Blueprint versions:

- `BP_IWS_MainHitMaker`
- `BP_IWS_InkHits`

These are kept as reference and compatibility assets. The C++ class was updated for better performance, but it keeps the same core formula used by the Blueprint implementation.

For new projects, prefer the optimized C++ path and use the Blueprint versions mainly as a learning reference.

## Runtime paintable data

Paintable meshes are stored as entries containing component references, hit arrays, render targets, and score data.

A paintable entry can contain:

- Target primitive component.
- Static mesh reference.
- Hit alpha values.
- Hit speeds.
- Hit locations.
- Hit normals.
- Hit sizes.
- Hit teams.
- Hit paint materials.
- Final-frame-only flags for distance optimization.
- Impact render target.
- Unwrap render target.
- Capture dirty state.
- Last captured transform.
- Team score data.

The hit arrays are processed together. When adding custom logic, keep these arrays aligned by index.

## Network behavior

`IWS Receive Impact` enters the C++ paint class and uses a server/multicast path:

- If the actor has authority, the hit is sent to the multicast registration path.
- If the actor does not have authority, it calls the server registration RPC.
- The server multicasts the hit so every client can register and process the same paint impact.

This keeps paint registration consistent in multiplayer-oriented setups.
