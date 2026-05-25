# FAQ

## Is `IWS_Ink_Hits` the full match manager?

No. `IWS_Ink_Hits` is the main optimized paint-hit class. It contains paint functions, utilities, replication entry points, and radial-hit processing. Match rules, session flow, scoring presentation, and gameplay-specific logic should be handled by the game framework or demo classes.

## Which class should be used as a reference in the demo?

Use `BP_IWS_PS_InkHits`. It is the class used by the demo and is derived from `BP_IWS_ZoneCheck`.

## Why are there Blueprint legacy classes?

`BP_IWS_MainHitMaker` and `BP_IWS_InkHits` are kept as legacy/reference Blueprint implementations. The C++ path was updated for better performance while preserving the same core formula.

## How can a weapon use a different paint visual?

Pass a different `Paint Material` into `IWS Receive Impact`. The optimized C++ pipeline uses the material provided by the impact call instead of relying on only one actor-level impact material.

## Should the Config category be changed?

Usually not much. The demo values used by `BP_IWS_PS_InkHits` are a good baseline. Change config values only when a project needs different render target resolution, material parameter names, or capture behavior.

## Why does the system have many optimization variables?

Painting uses render target operations and optional mesh surface captures. The optimization variables let projects balance visual responsiveness, CPU cost, GPU cost, and frame stability.

## Are the bots production ready?

No. The demo bots are simple and intended for testing. They should be replaced or extended for a production game.


## Does the default paint workflow support Nanite?

Nanite does not have full support in the default paint workflow. It can work in some cases, but Unreal Engine may recalculate Nanite geometry at runtime in ways that affect the paint bake step. This can create inconsistencies in the final visual result. For production paintable surfaces, use regular Static Mesh assets with Nanite disabled.

## Does the default paint workflow support Skeletal Mesh?

No. Skeletal Mesh components are not supported by the default paint workflow.

## Why does the system use UV channel 1?

UV channel 1 is used as the unwrap channel for the paint render target. Unreal commonly generates this channel as the lightmap UV channel, which usually provides separated islands suitable for paint projection. It also allows assets with multiple material slots to share one paint render target.

## What is `RT_Damage`?

`RT_Damage` is the render target texture parameter name expected by the material function and the paint system. If the material function is duplicated or recreated, keep this name unchanged and sample it with UV channel 1.

## What controls team colors in the demo?

The central Material Parameter Collection is `MPC_IWS_TeamColors`. In the demo, it is updated by the Game State at the beginning of the match. The Begin Play logic in `GS_InkWarfare` and `GM_InkWarfare_Gameplay` defines the active team color flow.
