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
