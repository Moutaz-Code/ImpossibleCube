# Impossible Cube

A cube where each face is a window into its own world. Each face writes its own value into the stencil buffer, and
each world is only drawn where that value is set, so you only ever see it through its face.

Made by Moutaz Pharaon in Unity 2022.3.62f3 with URP 14. It should work in Unity 6 (URP 17) but hasn't been tested there.

## Requirements

- URP.
- Smooth Shake Free by daburo (free; search for it on the Asset Store). It drives the cube's motion.
  Import it first, or the prefab shows a missing script.

## Install

Package Manager, then **+ › Add package from git URL…** and paste this repo's URL.

A git package is read-only. To edit the materials or prefab, clone the repo into your project's `Packages/` folder
instead, or copy what you need into `Assets/`.

## Setup (once per project)

1. **Layers.** In *Project Settings › Tags and Layers*, name layer **6** `StencilLayer1` and layer **7** `StencilLayer2`.
   The prefab's two inner worlds sit on layers 6 and 7. If those slots are already used, pick two free layers and move
   the prefab's world objects onto them.
2. **Renderer.** Open the Universal Renderer Data your URP asset uses.
   - In **Opaque Layer Mask**, untick `StencilLayer1` and `StencilLayer2`, so the worlds aren't drawn normally.
   - Add two **Render Objects** renderer features:

     | Setting | Feature 1 | Feature 2 |
     | :-- | :-- | :-- |
     | Event | After Rendering Opaques | After Rendering Opaques |
     | Filters › Queue | Opaque | Opaque |
     | Filters › Layer Mask | `StencilLayer1` | `StencilLayer2` |
     | Overrides › Stencil | On | On |
     | Stencil › Value | 1 | 2 |
     | Stencil › Compare Function | Equal | Equal |
     | Stencil › Pass / Fail / Z Fail | Keep | Keep |

3. Drag `Runtime/Prefabs/ImpossibleCube.prefab` into a scene.

## What's inside

- `Runtime/Shaders/StencilGeom.shader`: the face shader. It draws nothing (`Blend Zero One`, `ZWrite Off`) and writes
  `_StencilID` into the stencil buffer.
- `Runtime/Shaders/MoveyCubey.shadergraph`: the starry interior, sampling a cubemap by view direction.
- `Runtime/Textures/Starfield.png`: a generated star field, imported as a cubemap. If it shows up as a flat 2D
  texture, select it and set **Texture Shape** to **Cube**.

Cost: one extra filtered opaque draw per world, plus the invisible face quads.
