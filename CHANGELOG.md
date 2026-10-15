# Changelog

## 2.0.0

Version 2.0 turns the plugin into a general paint system. The paint can now be read back by gameplay code, it syncs to players who join late, it works with colors and amounts besides teams, and the demo gained two new modes and much smarter bots.

### Added

- **Paint queries.** The paint system keeps a small CPU copy of every paint mask, read from the GPU without stalling. `Query Paint At Location`, `Is Location Painted By Team`, `Is Actor On Team Paint`, `Get Painted Area By Team` and `Get Zone Paint` answer what is painted on floors, walls and ceilings. See [Paint Queries](Docs/PaintQueries.md).
- **Late join sync.** The server keeps the hits of the match and sends them to players who join later, in small chunks, through a sync proxy owned by each player. `Request Paint Resync` lets a client clear its paint and ask for everything again. See [Networking](Docs/Networking.md).
- **Spray demo** (`LVL_IWS_Demo_Spray`). First person spray cans with free colors per can, seven caps, intensity on the mouse wheel, a color picker, walk sway and a mist effect. The mask holds RGB color plus coverage in alpha instead of team channels.
- **Cleaning demo** (`LVL_IWS_Demo_Cleaning`). A water hose that washes dirt off walls and floors. The water flies in an arc, runs down walls, spills from walls to the floor and dries over time.
- **Paint tool components.** `IWS Paint Tool Component` (base), `IWS Spray Can Component` and `IWS Water Hose Component`. `Update Tool`, `Update Remote Tool` and `Can Use` are Blueprint native events, so a new tool can be made fully in Blueprint. See [Paint Tools and Demos](Docs/PaintToolsAndDemos.md).
- **New paint functions.** `Paint Color Hit`, `Fill Paintable`, `Fill Actors With Tag`, `Draw Material On Recent Paint` (paint that changes over time) and `Spill Paint Below` (paint running from a wall to the floor).
- **Save and load paint.** `Save Paint`, `Load Paint`, `Does Paint Save Exist`, `Delete Paint Save` and the `On Paint Loaded` event.
- **No Paint tag.** Actors or components with the tag `IWS.NoPaint` are left out of the paint.
- **Swim trails.** Swimmers push the paint and leave a wake of waves that spread and set in place, on floors and walls. New paint covers old trails. See [Trails, Splats and Drips](Docs/TrailsSplatsAndDrips.md).
- **Drips.** Optional team paint that runs down walls and lands on what is below (`Drips On Walls`, off by default).
- **Actor pool.** `IWS Actor Pool` world subsystem and `IWS Pooled Actor` base class. Bullets now come from the pool instead of being spawned. See [Actor Pool](Docs/ActorPool.md).
- **Bot AI helpers.** `Select Bot Zone`, `Find Paint Target In Zone`, `Find Combat Spot`, `Find Nearest Team Paint`, `Is Reachable By Walking`, `Find Climb Route` and the new `IWS Bot Climb Component`. See [Bots](Docs/Bots.md).
- **Performance report.** Console variable `iws.Perf` (overlay with game thread, render thread, memory and counts per part of the paint), `iws.PerfLog` for packaged builds, and `Get Paint Perf Report` for Blueprint. Paint parts are also visible in Unreal Insights and the CSV profiler.
- **Menu map choice.** The host picks the map in the main menu: Ink Battle, Spray or Cleaning. Menu and lobby show a Starting screen while the map opens and ignore a second press.

### Changed

- **Paint memory.** Each paint mask is sized from the mesh surface area (`Paint Texels Per Meter`, `Min/Max Paint Resolution`) and uses an 8 bit format by default. Unwrap textures of meshes that do not move are captured once and kept in a small cache, idle meshes give theirs back to a pool (`Max Cached Unwrap Textures`). Unwrap positions are stored relative to the mesh to keep precision far from the world origin.
- **Network traffic.** Hits travel in a compact struct and are grouped into one message per `Hit Replication Interval`, instead of one reliable call with a full hit result per hit. The unwrap and team score render targets are no longer replicated.
- **Scores.** Game score and zone score read the CPU copy of the paint and are weighted by painted area.
- **Player ground check.** The check that lets a player swim in its paint uses the paint query instead of a scene capture and a Niagara readback, and the paint next to the players is read right away.
- **Hit drawing.** Each hit is drawn only on the part of the mask it can reach (`Draw Hits In Bounds`). Hits out of view grow in a few big steps (`Step Hits Out Of View`, `Out Of View Hit Steps`), hits in view can grow in steps too (`In View Hit Steps`).
- **Battle splat.** The ink impact material shapes thick paint in 3D around the impact with a noisy edge and droplets, revealed as the hit grows. The cleaning water and the battle splash were rebuilt with material nodes and functions.
- **Shots.** Bullets collide from their first frame, ignoring the shooter and what is attached to it, so close shots no longer go through the floor.
- **Bots.** Bots fill the zones of their team, take back zones the enemy painted, invade when theirs are full, chase and strafe around enemies, refill by swimming in their paint and climb walls by painting them and swimming up. `Bot Think` is split into `Think Climb`, `Think Fight`, `Think Refill`, `Think Zone`, `Think Target` and `Move To Target`.
- **Demo Blueprints.** The demo characters are Blueprints, the demo HUDs are Widget Blueprints, and the graphs of the main classes were reorganized with comments on every block.
- `Recapture On New Hit` is now off by default. The capture of a mesh that only moves is refreshed by `Recapture When Transform Changes`.

### Removed

- Legacy Blueprint references `BP_IWS_MainHitMaker`, `BP_IWS_InkHits` and `BP_IWS_RadialHits`, plus the unused ground analysis assets and the old score sync.

### Upgrade notes from 1.x

- Custom paint materials must add the vector parameter `UnwrapOrigin` to the unwrap position read from `Unwrap_Tex`, since unwrap positions are now relative to the mesh. Use `M_IWS_RadialImpact` or `M_IWS_InkImpact` as reference.
- Projects that extended the legacy Blueprint classes should move to `IWS_InkHits` (C++) or `BP_IWS_PS_InkHits`.
- Paint masks are `RTF_RGBA8` by default now. A project that stores values above 1 in the mask needs `RTF_RGBA16f`, like the spray and cleaning demos do in `BP_IWS_PS_InkHits_Spray` and `BP_IWS_PS_InkHits_Cleaning`.
