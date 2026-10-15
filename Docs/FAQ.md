# FAQ

## Is `IWS_InkHits` the full match manager?

No. `IWS_InkHits` is the main paint class. It contains paint functions, paint queries, network sync, utilities and radial-hit processing. Match rules, session flow, scoring presentation, and gameplay-specific logic are handled by the game framework or demo classes.

## Which class should be used as a reference in the demo?

Use `BP_IWS_PS_InkHits`, the hit maker of the ink battle. It is a data-only child of `IWS_InkHits` with the demo values. `BP_IWS_ZoneCheck` is a separate actor: the zone used by bots and zone scores.

## Where are the Blueprint legacy classes?

`BP_IWS_MainHitMaker` and `BP_IWS_InkHits` were removed in 2.0. The C++ path keeps the same core formula.

## How can a weapon use a different paint visual?

Pass a different `Paint Material` into `IWS Receive Impact`. The pipeline uses the material provided by each impact.

## How can gameplay know what is painted?

Use the paint queries: `Query Paint At Location`, `Is Location Painted By Team`, `Is Actor On Team Paint`, `Get Zone Paint` and `Get Painted Area By Team`. See [Paint Queries](PaintQueries.md).

## Can the paint hold colors instead of teams?

Yes. Use `Paint Color Hit` or a paint tool component, a hit maker with a float mask, and a surface material that reads the mask your way. The spray demo stores RGB color plus coverage, the cleaning demo stores dirt and water. See [Paint Tools and Demos](PaintToolsAndDemos.md).

## Do players who join a match late see the paint?

Yes, with `Sync Paint To Late Joiners` on. The server sends the hits of the match to the new player. See [Networking](Networking.md).

## How can a mesh be kept clean?

Add the tag `IWS.NoPaint` to the actor or to the component.

## Can the paint be saved?

Yes, with `Save Paint` and `Load Paint`. It saves the masks of the level on the machine that calls it.

## Should the Config category be changed?

Usually not much. The demo values used by `BP_IWS_PS_InkHits` are a good baseline. Change config values when a project needs different mask sizes, formats, memory limits or material parameter names.

## Why does the system have many optimization variables?

Painting uses render target operations, surface captures and readbacks. The variables let projects balance visual responsiveness, CPU cost, GPU cost, memory and frame stability. `iws.Perf 1` shows which part costs the most.

## Are the bots production ready?

No. They paint, fight, refill and climb, but they are a demo and testing tool. Extend or replace them for a production game.

## Does the default paint workflow support Nanite?

Nanite does not have full support in the default paint workflow. It can work in some cases, but Unreal Engine may recalculate Nanite geometry at runtime in ways that affect the paint bake step. This can create inconsistencies in the final visual result. For production paintable surfaces, use regular Static Mesh assets with Nanite disabled.

## Does the default paint workflow support Skeletal Mesh?

No. Skeletal Mesh components are not supported by the default paint workflow.

## Why does the system use UV channel 1?

UV channel 1 is used as the unwrap channel for the paint mask. Unreal commonly generates this channel as the lightmap UV channel, which usually provides separated islands suitable for paint projection. It also allows assets with multiple material slots to share one paint mask.

## What is `RT_Damage`?

`RT_Damage` is the texture parameter name of the paint mask, set by the paint system on every painted mesh. If the material function is duplicated or recreated, keep this name unchanged and sample it with UV channel 1. Meshes with swim trails also get `RT_Trail`.

## My custom paint material from 1.x paints in the wrong place.

Since 2.0 the unwrap stores positions relative to the mesh. Add the vector parameter `UnwrapOrigin` to the value read from `Unwrap_Tex` to get the world position. See [Material Setup](MaterialSetup.md#creating-a-custom-paint-hit-material).

## What controls team colors in the demo?

The central Material Parameter Collection is `MPC_IWS_TeamColors`. In the demo, it is updated by the Game State at the beginning of the match. The Begin Play logic in `GS_InkWarfare` and `GM_InkWarfare_Gameplay` defines the active team color flow.
