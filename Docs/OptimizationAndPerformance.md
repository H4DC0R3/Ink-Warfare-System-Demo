# Optimization and Performance

The C++ paint path exposes performance controls under the `IWS|Radial Hits` and `IWS|Config|Memory` categories, and three ways to see what the paint costs: the `iws.Perf` report, Unreal's `stat` groups, and Unreal Insights or the CSV profiler. Use this page to tune the budgets, find the expensive step, and check memory.

<p align="center">
  <img src="../Images/IMG_OptimizationVars.png" alt="Radial Hits optimization and distance variables" width="900" />
</p>

## Paint performance report: `iws.Perf`

New in 2.0. Open the console and run:

```text
iws.Perf 1
```

| Value | Effect |
| --- | --- |
| `0` | Off. Nothing is measured. |
| `1` | Measures the paint and shows its costs on screen. |
| `2` | Measures without the overlay. Read the numbers with `Get Paint Perf Report`. |

The report covers the last half second. It shows the frame (FPS, frame, game thread, render thread and GPU time), the total cost of the paint on the game thread and on the render thread, how many draws each hit needed, the memory of the masks, trails and CPU copy, and the counts of paintables, active hits, cached unwraps and trails.

Below that it splits the paint into parts, each with game thread time, render thread time and calls per second:

| Part | What it is |
| --- | --- |
| `Update` | The radial hit update. Its sub-parts are `Hit draws`, `Trail erase`, `Trail waves`, `Captures` and `Drips`. |
| `Register hits` | Receiving and registering hits. |
| `Trail push` | `Paint Trail` calls. |
| `Paint queries` | All paint queries and bot helpers. |
| `Readbacks` | Reading the paint back to the CPU. |
| `Score` | Game and zone scores. |
| `Spill` | `Spill Paint Below`. |
| `Over time` | `Draw Material On Recent Paint`. |
| `Paint tools` | Spray can and water hose components. |

The entry points add up to the total of the paint. In standalone and packaged builds, `iws.PerfLog 5` writes the report to the log every 5 seconds while `iws.Perf` is on.

From Blueprint: `Set Paint Perf Mode`, `Get Paint Perf Mode` and `Get Paint Perf Report` (`ST_IWS_PaintPerfReport`), for a custom HUD or automated tests.

The same parts are timed as `IWS_*` scopes in Unreal Insights and as the `IWS` category of the CSV profiler.

## Stat overlay: `stat IWSRadialHits`

The plugin also registers the Unreal stats group **IWS Radial Hits**.

<p align="center">
  <img src="../Images/IMG_PerformanceScreen.png" alt="IWS Radial Hits performance overlay" width="900" />
</p>

```text
stat IWSRadialHits
```

Run it again, or `stat none`, to hide it.

| Area | What it shows | How to read it |
| --- | --- | --- |
| `Cycle counters` | Timed CPU scopes for each step of the radial hit system. | `InclusiveAvg` is the cost of a step including nested work, `ExclusiveAvg` without children. `Max` shows spikes. |
| `Counters` | Runtime counts, budgets and memory. | How much work is active, how much was processed, and whether a budget is delaying captures or draws. |

The stats are reset when the hit maker starts and ends play, so an old Play-In-Editor session does not mix with a new one.

### Average group

```text
stat IWSRadialHitsAverage
```

Long-running averages of the same steps in microseconds (`Average ... us`, `1000 us` is `1 ms`). Useful to compare settings, since it smooths out short spikes.

### Cycle counters

| Counter | Meaning | Typical optimization target |
| --- | --- | --- |
| `Update Total` | Full cost of one radial hit update. | Main number for the whole system. |
| `Register Hit` | Receiving and registering a new hit. | High when many hits arrive at once. |
| `Distance Check` | Whether a hit animates by distance. | Usually very small. |
| `Find Paintable` | Finding the paintable of the hit component. | High with many paintables. |
| `Ensure Render Targets` | Validating or creating masks. | Low after the first hit on each mesh. |
| `Create Render Target` | Allocating a missing mask or unwrap. | Expensive in combat. Use `Fill Paintable` to prepare meshes before. |
| `Capture Total` | Full unwrap capture of a paintable. | One of the main costs. Static meshes are captured once. |
| `Capture Adjust Camera` | Positioning the capture camera. | Usually small. |
| `Capture Create Mesh MI` | Preparing material instances for capture. | Should not repeat after setup. |
| `Capture Toggle Unwrap Material` | Switching the mesh to the unwrap material. | Grows with many material slots. |
| `Capture Mark Render State Dirty` | Forcing the render state update before capture. | Disable only if captures stay correct without it. |
| `Capture Scene` | The `SceneCaptureComponent2D` capture itself. | Often expensive. |
| `Capture Revert Materials` | Restoring the original materials. | Grows with many material slots. |
| `Get Hit Material Instance` | Retrieving the hit material instance. | Low unless many paint materials are used. |
| `Apply Hit Total` | Applying one hit to the mask. | Parameters plus draw. |
| `Set Hit Material Parameters` | Sending hit data to the material. | Usually small. |
| `Draw Material To RT` | Drawing the hit material into the mask. | Main runtime cost with many splashes. |
| `Remove Hit` | Removing finished hits. | High when many hits finish together. |

### Counters

| Counter | Meaning | Tuning note |
| --- | --- | --- |
| `Registered Hits Total` / `Removed Hits Total` | Hits registered and finished since play started. | Confirms shots reach the system. |
| `Paintables` / `Active Paintables` | Meshes known by the system, and meshes with hits waiting. | Many active paintables: raise budgets or lower hit volume. |
| `Active Hits` | Hits currently animating. | Too high: shorten hits or use steps out of view. |
| `Draws Last Update` | Draw operations in the last update. | Compare with `Max Draws Per Update`. |
| `Processed Hits Last Update` / `Processed Paintables Last Update` | Work done in the last update. | Compare with the per update budgets. |
| `Captures Last Update` / `Captures Total` | Unwrap captures. | Should stop growing once meshes are captured. |
| `Invalid Paintables Last Update` | Paintables skipped because their component is gone. | Normally `0`. |
| `Skipped Capture Budget Last Update` | Paintables waiting because the capture budget was used. | Raise `Max Captures Per Update` if paint is delayed. |
| `Skipped Missing Capture Last Update` | Paintables skipped without a valid capture. | Capture failed or was postponed. |
| `Paint Render Targets` / `Paint Render Target Memory` | Masks and their memory. | Lower `Paint Texels Per Meter` or `Max Paint Resolution` if high. |
| `Cached Unwrap Textures` | Unwrap textures in memory. | Limited by `Max Cached Unwrap Textures`. |
| `Paint Readbacks Total` / `Paint Readbacks Near Players Total` | Reads of the paint back to the CPU. | See [Paint Queries](PaintQueries.md). |
| `Paint Query Memory` | Memory of the CPU copy. | Raise `Paint Query Downscale` to lower it. |
| `Hits Drawn In Bounds Total` / `Hits Drawn Full Texture Total` | Draws limited to the hit area, and draws over the whole mask. | Most should be in bounds. |
| `Out Of View Draws Saved Total` | Draws saved by `Step Hits Out Of View`. | Shows how much the steps help. |

## Optimization variables

| Variable | Description | Performance note |
| --- | --- | --- |
| `Auto Update Radial Hits` | Updates radial hits with a timer. | Disable only if another system calls `Update Radial Hits`. |
| `Radial Hits Update Interval` | Seconds between automatic updates. `0.033` is about 30 per second. | Lower is smoother and more expensive. |
| `Stop Radial Hits Timer when Idle` | Stops the timer when no hit is waiting. | Recommended. |
| `Max Paintables Per Update` | Paintables processed per update. `0` means no limit. | Spreads work across frames. |
| `Max Hits Per Paintable Per Update` | Hits processed per paintable per update. `0` means no limit. | Useful when many shots hit the same mesh. |
| `Capture Paintable Every Update` | Captures before every draw. | Safest but expensive. Keep off. |
| `Capture Only when Dirty` | Captures only when needed. | Recommended. |
| `Recapture on New Hit` | Recaptures the mesh on every new hit. | Only for meshes that deform. Off by default since 2.0. |
| `Mark Render State Dirty Before Capture` | Forces the render state update before capture. | Avoids stale data, has a cost. |
| `Recapture when Transform Changes` | Recaptures a mesh that moved beyond the tolerance. | Static meshes are captured only once. |
| `Transform Recapture Tolerance` | Smallest change that triggers a recapture. | Higher ignores small movement. |
| `Max Captures Per Update` | Captures per update. `0` means no limit. | One of the main controls. |
| `Max Draws Per Update` | Draws per update. `0` means no limit. | Lower reduces spikes but delays hits. |
| `Use Swap Remove for Expired Hits` | Removes finished hits with swap-remove. | Faster. Disable only to debug hit order. |
| `Draw Hits in Bounds` | Draws each hit only on the part of the mask it can reach. Needs `Enable Paint Queries`. | Large saving on big masks. |
| `Hit Bounds Scale` | Scales the area drawn by each hit. | Raise if a custom paint material looks cut. |

## Distance variables

| Variable | Description | Performance note |
| --- | --- | --- |
| `Use Distance Based Hit Update` | Uses the viewer distance to decide how a hit animates. | Recommended. |
| `Full Animation Distance` | Distance within which hits animate normally. | Farther keeps more hits fully animated. |
| `Apply Far Hits as Final Frame` | Far hits are drawn directly at their final size. | Fewer draws for distant hits. Off in the demo, which uses steps instead. |
| `Step Hits Out Of View` | Hits outside the view of every local player grow in a few big steps. A hit goes back to the full animation as soon as a player looks at it. | Same paint with fewer draws. |
| `Out Of View Hit Steps` | Steps of a full intensity hit out of view. | More steps are closer to the full animation. |
| `In View Hit Steps` | Steps of a full intensity hit in view. `0` draws on every update. | The demo uses `8`. |
| `Out Of View Margin Degrees` | Degrees added around the field of view. | Covers screen corners and fast turns. |

## Memory variables

<p align="center">
  <img src="../Images/IMG_ConfigMemoryVars.png" alt="Config and Memory variables" width="900" />
</p>

| Variable | Description |
| --- | --- |
| `Paint Render Target Format` | Format of the masks. `RTF_RGBA8` uses half the memory of `RTF_RGBA16f`. |
| `Unwrap Render Target Format` | Format of the unwrap. `RTF_RGBA32f` only for very large meshes that need more precision. |
| `Paint Texels Per Meter` | Mask texels per meter of mesh surface. |
| `Min Paint Resolution` / `Max Paint Resolution` | Smallest and largest mask. |
| `Max Cached Unwrap Textures` | Unwrap textures kept at once. `0` means no limit. |

## Recommended baseline

The values of `BP_IWS_PS_InkHits` are a good baseline:

| Setting | Demo value |
| --- | --- |
| `Radial Hits Update Interval` | `0.033` |
| `Stop Radial Hits Timer when Idle` | Enabled |
| `Capture Only when Dirty` | Enabled |
| `Recapture on New Hit` | Disabled |
| `Max Captures Per Update` | `0` |
| `Max Draws Per Update` | `60` |
| `Draw Hits in Bounds` | Enabled |
| `Use Distance Based Hit Update` | Enabled |
| `Apply Far Hits as Final Frame` | Disabled |
| `Step Hits Out Of View` | Enabled, `8` steps |
| `In View Hit Steps` | `8` |
| `Paint Render Target Format` | `RTF_RGBA8` |
| `Paint Texels Per Meter` | `64` |
| `Max Cached Unwrap Textures` | `24` |

## Tuning guide

If paint is too expensive:

1. Run `iws.Perf 1` and find the part with the highest game or render thread time.
2. If it is `Hit draws`, lower `Max Draws Per Update`, raise `In View Hit Steps` or `Out Of View Hit Steps`, and keep `Draw Hits in Bounds` on.
3. If it is `Captures`, keep `Capture Paintable Every Update` and `Recapture on New Hit` off, lower `Max Captures Per Update`, and prepare meshes with `Fill Paintable`.
4. If it is `Readbacks` or `Paint queries`, raise `Paint Query Downscale` or lower `Max Paint Readbacks Per Second`.
5. Raise `Radial Hits Update Interval`.

If paint appears delayed:

1. Check `Skipped Capture Budget Last Update`, `Draws Last Update` and `Processed Hits Last Update` in `stat IWSRadialHits`.
2. Lower `Radial Hits Update Interval`.
3. Raise `Max Draws Per Update`, `Max Hits Per Paintable Per Update` and `Max Paintables Per Update`.
4. Raise `Max Captures Per Update` only if capture budget skips are visible.

If memory is too high:

1. Check `Paint Render Target Memory` and `Paint Query Memory`.
2. Lower `Paint Texels Per Meter` or `Max Paint Resolution`.
3. Keep `RTF_RGBA8` unless the mask must hold values above 1.
4. Lower `Max Cached Unwrap Textures`.

If moving meshes paint incorrectly:

1. Enable `Recapture when Transform Changes`.
2. Adjust `Transform Recapture Tolerance`.
3. Keep `Mark Render State Dirty Before Capture` enabled.

## Important performance concept

A capture is usually more expensive than drawing a hit. Static meshes are captured once and their unwrap is kept in the cache. For moving paintable objects, recapture only when the transform changes enough to matter.
