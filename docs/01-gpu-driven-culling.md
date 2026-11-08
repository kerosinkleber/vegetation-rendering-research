# 01: GPU-driven culling, instancing and indirect draws

[← Back to README](../README.md)

Dense vegetation means hundreds of thousands of instances. Issuing one CPU draw call per object doesn't scale, so modern engines move the visibility decision to the GPU.

## The pattern

1. Split meshes into small **clusters** (~64 triangles).
2. A compute shader culls **instances**: frustum, then occlusion.
3. A second pass culls **clusters**: frustum, occlusion, triangle backface.
4. Compact the surviving index data.
5. Submit everything with **one or a few indirect multi-draws**.

## Assassin's Creed Unity (Ubisoft): **High**

- Draw calls cut by **1-2 orders of magnitude**.
- About **10× the objects** of the previous AC game, at about **75% of its CPU cost**.
- The GPU-side triangle savings were smaller. Cluster culling removed 20-40% of triangles in the main view (under a 10% overall geometry gain), but **30-80% of shadow triangles**.

Source: Haar & Aaltonen, *GPU-Driven Rendering Pipelines*, SIGGRAPH 2015 Advances (slides 9, 12, 36-37).

## Two-phase Hi-Z occlusion (RedLynx): **High**

1. Cull against the **previous frame's** depth pyramid.
2. Render the survivors, rebuild the pyramid, then **retest the culled objects** to catch false negatives such as newly visible objects.

Result: **250k objects** in **2.3 ms GPU** (culling plus G-buffer) and **0.2 ms CPU** on one Jaguar core (Xbox One), with only **two `DrawInstancedIndirect` calls**.

Source: same SIGGRAPH 2015 deck.

## Saber Engine (World War Z): **High**

- About **0.7 ms GPU budget** on Xbox One for 100k+ objects. That is a budget, not a measured cost.
- Main camera: Hi-Z occlusion against hand-made occluders. Shadow cascades: frustum culling only.
- One frame of latency for the CPU readback.

Source: GDC 2019, *High Zombie Throughput in Modern Graphics*.

## Frostbite: **High** (abstract only)

GDC 2016 *Optimizing the Graphics Pipeline with Compute* took the same compute-culling approach to push triangle throughput past fixed-function limits. The verified page gives no numbers.

## Recent AAA: no special foliage path

- **Alan Wake 2 (Northlight):** "No custom systems for grass or foliage." Vegetation runs through the generic GPU cluster/set/bucket pipeline, with alpha-tested geometry kept in its own bucket. Culling had to take occlusion from alpha-tested and deformed geometry. Vegetation deformation reuses the generic compute-skinning path. **High.** (REAC 2024)
- **Ghost of Yōtei (Sucker Punch):** chains of compute jobs do occlusion culling, memory allocation and draw-record generation, then the CPU reads back and builds command lists, so it is a hybrid. In a distant mountain shot, **more than 1 million trees, rocks and bushes are culled to about 60k items**. **High.** (PlayStation Blog, Oct 2025)

## Takeaways

- Cull on the GPU at instance level first, then cluster level.
- Use the previous frame's depth for occlusion, then re-test the culled objects.
- Shadows benefit most from cluster culling (30-80% of triangles removed).
- The trend is a single generic GPU-driven pipeline that handles foliage too, not a bespoke foliage renderer.
