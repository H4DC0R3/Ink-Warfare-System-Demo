# Getting Started

This guide explains the minimum setup required to understand and test the Ink Warfare System demo.

## Requirements

- Unreal Engine version supported by the Fab package.
- A project with the Ink Warfare System plugin enabled.
- At least one supported Static Mesh that should receive paint.
- UV channel 1 on paintable meshes.
- A material based on `M_IWS_DefaultAsset` or another material that uses the Ink Warfare material function.
- A gameplay object, projectile, weapon, or trace system that can call `IWS Receive Impact`.

## Basic setup flow

1. Enable the Ink Warfare System plugin.
2. Restart the editor when Unreal asks for it.
3. Open the demo map or place the paint manager actor used by the demo.
4. Make sure paintable meshes use the materials and setup expected by the system.
5. On hit, call `IWS Receive Impact` on the actor responsible for the paint system.
6. Pass the `Hit Result`, hit size, intensity, speed, team, and paint material.
7. Play the map and verify that the impact appears on the target mesh.

## Main demo classes

The demo uses a Blueprint class named `BP_IWS_PS_InkHits` as the main paint-processing actor.

`BP_IWS_PS_InkHits` is derived from `BP_IWS_ZoneCheck`. It contains the main demo loop that registers paintable assets and drives the paint update flow used by the demo.

There is also a legacy Blueprint class named `BP_IWS_InkHits`. It exists for compatibility and reference, but the optimized C++ path is the recommended implementation for production use.

## Main C++ class

`IWS_Ink_Hits` is the main C++ class used by the optimized paint pipeline. It does not update the full match rules by itself. Instead, it contains the paint functions, replicated impact entry point, render-target utilities, and radial-hit processing helpers used by other gameplay classes.

A full Blueprint version, `BP_IWS_MainHitMaker`, is available as a legacy reference. The optimized C++ implementation was introduced for better runtime performance while keeping the same core formula used by the original Blueprint version.

## Recommended learning order

1. Read [Painting System Overview](PaintingSystemOverview.md).
2. Read [IWS Receive Impact](IWSReceiveImpact.md).
3. Read [Ink Hits Classes](InkHitsClasses.md).
4. Review [Optimization and Performance](OptimizationAndPerformance.md).
5. Open the demo Blueprint classes and compare them with the documentation.
