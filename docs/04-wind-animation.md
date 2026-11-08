# 04: Cheap wind animation

[← Back to README](../README.md)

The core idea: do the expensive work **per instance** rather than per vertex, and scale wind complexity down with importance and distance.

## Crysis (Crytek): **High**

- One **world-space wind vector per vegetation instance**, summed from all wind sources on the CPU. Per-vertex cost stays constant no matter how many wind sources there are; the CPU cost per instance grows linearly with the number of sources.
- **Main bending:** driven by the xy components of the wind vector.
- **Detail bending:** driven by vertex colors:
  - R = leaf edge stiffness
  - G = phase offset
  - B = overall stiffness
  - A = ambient occlusion

Source: GPU Gems 3, ch. 16, *Vegetation Procedural Animation and Shading in Crysis* (2007).

## SpeedTree: **High**

- Keep **several vertex-shader wind variants**: complex for hero trees, simpler for shrubs.
- **Fade complex wind to simple sway at distance.** SpeedTree says this "greatly" reduces average vertex shader cost. It's a suggested change, not the reference application's default.

Source: SpeedTree Runtime SDK, *GPU Performance* docs.

## Pivot Painter 2.0 (Unreal): **High**

- Stores each sub-element's **pivot, rotation, extent and basis data in baked textures**, read through **one extra UV channel**.
- Wind is generated in the vertex shader (`PivotPainter2FoliageShader`).
- Hierarchies up to **4 levels deep** and **30,000 elements**. Storing integers as floats with bit-shifting raised the limit from 3,000.
- Epic says it's far cheaper than skeletal animation, but the textures and vertex texture fetches add memory and GPU cost.

Source: Epic, *Pivot Painter Tool 2.0* docs.

## Nanite Skinning (UE5): **Medium**

- Wind uses **skeletal bones** instead of World Position Offset (WPO), which keeps cluster bounds tight and reduces overdraw and culling problems.
- About **0.1 ms GPU per 100k bones**.
- Animation turns off below a screen-size threshold.

Source: Epic, *Nanite Foliage* docs. Epic's own numbers.

## Takeaways

- Compute wind once per instance and keep per-vertex work cheap and constant.
- Bake hierarchy data (pivots, stiffness) into vertex colors or textures.
- Use cheaper wind shaders in the distance, and stop animating entirely when the object is tiny on screen.
- On a Nanite or cluster renderer, avoid vertex offsets that break cluster bounds.
