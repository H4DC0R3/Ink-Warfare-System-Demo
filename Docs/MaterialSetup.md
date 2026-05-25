# Material Setup

Ink Warfare System is designed to let each project define its own final surface shader while keeping the paint filtering and team-color logic centralized.

The plugin includes a specific material function for the paint workflow. This function handles the internal filtering of the paint data and exposes outputs that can be used to build a shader that matches the visual style of the project.

<p align="center">
  <img src="../Images/IMG_MaterialFunction.png" alt="Ink Warfare material function reference" width="900" />
</p>

## Default demo material

The demo material `M_IWS_DefaultAsset` is the recommended reference for new integrations.

It shows a simple and direct example of how to use the material function outputs with a mesh material. When creating a custom material, use this material as the first reference before building a more complex shader.

## Main material function behavior

The material function reads the paint render target and prepares the paint information for the final material.

The most important output for standard materials is the mix output. This output blends between the received base color and the paint colors assigned to each team.

Typical usage:

1. Start with the asset's normal base color.
2. Read the paint data through the Ink Warfare material function.
3. Use the mix output to blend the base color with the team paint colors.
4. Continue building the rest of the material normally, such as roughness, normal, and other visual effects.

## Important parameter: `RT_Damage`

Inside the material function, the most important texture parameter is:

```txt
RT_Damage
```

This is the render target parameter name that the paint system looks for.

If you duplicate or recreate the material function, keep this exact parameter name. Renaming it will break the default lookup expected by the system.

`RT_Damage` must be sampled using **UV channel 1**, because UV 1 is the unwrap channel used by the paint system.

## Material function outputs

The material function includes outputs for more than one use case.

Common outputs include:

- Final color mixing between the original base color and team paint colors.
- Team paint masks.
- Pure team color reads.
- Masked paint layers.
- Helper data for additional visual effects.
- Separation data that can be used for normals or edge details between paint regions.

Not every material needs to use every output. A simple material can use only the mix output, while a more stylized material can use the additional masks and helper outputs to create stronger ink borders, wetness, grooves, roughness changes, or custom effects.

## Team colors material parameter collection

Team colors are centralized in:

```txt
MPC_IWS_TeamColors
```

This Material Parameter Collection is updated by the Game State at the beginning of the match. It receives the active colors for the teams and becomes the central point used by systems that need to read the current match colors.

Examples of systems that may use this collection:

- Player materials.
- Paintable asset materials.
- UI materials.
- VFX materials.
- Gameplay feedback materials.

Changing this Material Parameter Collection directly affects visuals that read from it, but it does not directly change gameplay rules. Gameplay ownership, score, and team logic are still driven by the gameplay systems.

To understand the demo flow that defines which color each team uses, check the Begin Play logic in:

```txt
GS_InkWarfare
GM_InkWarfare_Gameplay
```

## Creating a custom paint material

Recommended workflow:

1. Duplicate `M_IWS_DefaultAsset`.
2. Keep the Ink Warfare material function connected.
3. Keep the `RT_Damage` parameter name unchanged.
4. Make sure the render target is sampled with UV channel 1.
5. Replace or expand the base material logic to match the project art direction.
6. Use the mix output for the first working version.
7. Add extra outputs only after the base paint behavior is confirmed.

## Practical notes

The paint material used by an impact can also be customized through the impact call. This allows different projectiles or gameplay effects to use different paint visuals while still using the same general paint system.

For most projects, the default `M_IWS_DefaultAsset` graph is the safest starting point.
