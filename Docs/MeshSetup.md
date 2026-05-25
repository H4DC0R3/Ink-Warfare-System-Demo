# Mesh Setup

This page explains how to prepare meshes for the Ink Warfare System paint workflow.

## Supported mesh type

The paint system is designed for supported **Static Mesh** assets.

Current limitations:

- Nanite meshes do not have full support in the default paint workflow. They may work in some cases, but the way Unreal Engine recalculates Nanite geometry at runtime can cause issues during the paint bake step, which may create inconsistencies in the final visual result.
- Skeletal Mesh components are not supported by the default paint workflow.
- The mesh must use a material that reads the paint render target through the Ink Warfare material function.

For reliable results, use regular Static Mesh assets with Nanite disabled on paintable surfaces. If a mesh paints inconsistently, first verify that it is a regular Static Mesh, that Nanite is disabled, and that the material is using the Ink Warfare material function correctly.

## UV channel requirement

By default, paintable meshes must have **UV channel 1**.

<p align="center">
  <img src="../Images/IMG_UV1.png" alt="Static mesh UV channel 1 reference" width="900" />
</p>

UV channel 1 is used by the system as the unwrap channel for the paint capture and render-target workflow.

In most Unreal Engine projects, UV channel 1 is already generated as the lightmap UV channel. This usually makes the setup close to plug-and-play, because Unreal's generated lightmap UVs normally contain separated islands suitable for this type of unwrap workflow.

## Why UV 1 is important

The main purpose of using UV channel 1 is to make the paint target independent from the visible material layout.

This is especially important for assets that use multiple material slots. With the UV 1 workflow, the asset can be painted using a single paint render target for the whole mesh, instead of requiring one paint texture per material slot.

In practice, this means:

- One paint render target can represent the paint state of the asset.
- Assets with multiple material slots can still share the same paint data.
- The visible materials can remain customized for the project.
- The paint layer is driven by the unwrap data instead of the visible UV layout.

## Recommended mesh checklist

Before testing an asset, verify:

1. The asset is a Static Mesh.
2. Nanite is disabled for the asset when consistent paint baking is required.
3. The mesh has UV channel 1.
4. UV channel 1 has valid separated islands.
5. The mesh material uses the Ink Warfare material function.
6. The actor is registered by the paint hit system used in the level.
7. The gameplay hit calls `IWS Receive Impact` with a valid hit result.

## Common issues

### The mesh does not paint

Check that the mesh has UV channel 1 and that the material uses the expected paint render target parameter.

### Paint appears on unexpected parts of the mesh

Check the UV channel 1 unwrap. Overlapping islands can cause different surface areas to share the same paint data.

### Only some material slots seem to react

Make sure every visible material slot that should show paint uses the Ink Warfare material function or an equivalent shader setup.
