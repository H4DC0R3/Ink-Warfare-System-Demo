# IWS Receive Impact

`IWS Receive Impact` is the main Blueprint-facing entry point for sending a gameplay hit into the paint system.

<p align="center">
  <img src="../Images/IMG_HitMessage.png" alt="IWS Receive Impact Blueprint node" width="420" />
</p>

## When to call it

Call `IWS Receive Impact` when a gameplay event should create team paint. Common examples:

- Projectile impact.
- Weapon trace hit.
- Explosion splash.
- Ability impact.
- Character or object interaction that should paint a surface.

For paint that is not owned by a team, like a color or an amount of dirt, use `Paint Color Hit` or a paint tool component instead. See [Paint Tools and Demos](PaintToolsAndDemos.md).

## Inputs

| Input | Description |
| --- | --- |
| `Target` | Object that implements the `IWS Gameplay Interface`. Usually the hit maker (`BP_IWS_PS_InkHits`). |
| `Hit Result` | The Unreal hit result produced by a trace, projectile, or collision event. It must contain a valid static mesh component. |
| `Size` | Hit size range. The X and Y values are used as the radius range during the paint animation. |
| `Intensity` | Initial hit strength. A value of `1.0` is normally a full-strength hit. |
| `Speed` | Animation speed for the radial hit. Higher values make the splash reach its final state faster. |
| `Team` | Team that owns the paint. The system maps teams to mask channels and score ownership. |
| `Paint Material` | Material used to draw the hit. Change this to customize the visual shape and behavior of each impact type. |

## Customizing the visual result

The most important customization point is the `Paint Material` input.

Different weapons can pass different materials while still using the same paint pipeline. For example:

- `MI_IWS_InkImpact`: the battle splat, thick paint with a noisy edge and droplets.
- `M_IWS_RadialImpact`: a simple round hit, the best starting point for a custom material.
- Larger splash, soft stain, sharp paintball or special ability materials made from them.

The paint material receives runtime parameters from the C++ hit processor, including color, intensity, radius, hit position, hit normal, unwrap texture and unwrap origin. The list is in [Painting System Overview](PaintingSystemOverview.md#paint-material).

## Recommended usage pattern

A typical hit flow is:

1. Perform a line trace or receive a projectile hit.
2. Validate that the hit component is a paintable static mesh.
3. Get the hit maker with `Get Hit Maker`, or keep a reference to it.
4. Call `IWS Receive Impact`.
5. Pass the hit result, size, intensity, speed, team, and paint material.

The demo bullet `BP_IWS_PaintBullet_Type_01` is the reference. It comes from the [Actor Pool](ActorPool.md). When it lands, its `Hit Affected Walls` function runs a sphere trace around the impact and calls `IWS Receive Impact` once for every mesh it touches, so a splat continues over corners and onto the meshes next to the one that was hit. Meshes tagged `CantPaint` are skipped by the bullet.

## Notes

- The target should implement `IWS Gameplay Interface`.
- The paint material should be valid. Hits with an invalid paint material are ignored.
- The hit component should be a static mesh component without the `No Paint Tag` (`IWS.NoPaint`).
- In multiplayer, the server paints the hit and sends it to the clients in the next batch. Clients can call the node too, it is routed to the server.
