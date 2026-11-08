# 03: Grass and procedural placement

[← Back to README](../README.md)

## Horizon Zero Dawn: GPU procedural placement (Decima): **High**

- **All nature** is placed procedurally on the GPU while the player moves: **500+ asset types, 100k+ objects** in the scene.
- Cost: about **250 µs average GPU busy load**.
- Placement is driven by **WorldData**: streamed 2D maps at 0.5-2.0 m resolution, mostly BC7, either generated or hand-painted, at about **4 MB per km²**.
- The maps are artist-authored rule inputs, so placement isn't hands-free.

Sources: van Muijden, *GPU-Based Procedural Placement in Horizon Zero Dawn*, GDC 2017 ([PDF](https://www.guerrilla-games.com/media/News/Files/GDC2017_VanMuijden_GPUBasedProceduralPlacementInHorizonZeroDawn.pdf)).

## Horizon Forbidden West: **High**

Foliage and alpha-tested geometry go through a visibility-buffer and deferred-texturing system. See [05](05-overdraw-and-alpha.md).

## Ghost of Tsushima: per-blade GPU grass: **Medium**

- Individual grass blades are **generated on the GPU** each frame, each with its own procedural appearance and animation, instead of authored grass cards. This covers acres of grass within memory and performance budgets.
- The GDC abstract gives **no blade counts, memory figures or timings**.
- Secondhand write-ups (not verified) describe a 15-vertex high-LOD blade and a 7-vertex low-LOD blade with vertex blending between them, and wider, sparser blades in the distance.
- Clumping, density falloff and per-frame cost are **unverified**.

Talk: Eric Wohllaib, *Procedural Grass in Ghost of Tsushima*, GDC 2021, on [GDC Vault](https://gdcvault.com/play/1027214/) and [YouTube](https://www.youtube.com/watch?v=Ibe1JBF5i5Y).

## Ghost of Yōtei: **High**

- The GPU compute renderer's **grass and renderable-item budget was doubled**, most likely relative to Tsushima.
- More than 1M trees, rocks and bushes are culled to about 60k. See [01](01-gpu-driven-culling.md).

Source: Adrian Bentley, PlayStation Blog, Oct 2025.

## Saber Engine: grass as spatial batches: **Medium**

- Blades placed with a "seed brush" are grouped into **containers**.
- Each container holds per-blade data (position and orientation, indirect lighting sampled from the lightmap, an animation seed) plus **one OBB** used for culling and LOD, and draws in **one call**.
- The system was robust enough to reuse for a background swarm of 5k+ zombies.

Source: GDC 2019, Saber Interactive.

## Takeaways

- Store **rules and density maps**, not instances. At about 4 MB/km² this is far cheaper than per-instance data.
- Generate near-field grass per blade on the GPU, and reduce vertex count and density with distance.
- Batch grass into spatial chunks with one bounding volume each, for culling and LOD.
