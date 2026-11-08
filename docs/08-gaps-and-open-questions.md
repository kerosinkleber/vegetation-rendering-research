# 08: Gaps and open questions

[← Back to README](../README.md)

Two research passes did **not** produce verified evidence on the following.

## Missing data

- **Alpha-to-coverage:** no measured cost or benefit.
- **Dithered or temporal LOD crossfade:** no measured cost.
- **Mesh-shader foliage cards** and tighter leaf-card outlines: no measured overdraw savings.
- **Geometry-shader vs compute-shader grass:** no direct comparison.
- **Virtual Shadow Maps with animated foliage:** only forum threads on WPO wind invalidating the cache.
- **Impostors vs Nanite Voxels at 1-5 km:** no GPU-time or memory comparison.
- **Ghost of Tsushima:** blade counts, clumping, density falloff and per-frame cost aren't confirmed beyond the GDC abstract.
- **Frostbite and Unity HDRP** vegetation systems.
- **Godot:** no benchmarks for any plugin; the YouTube list is unverified.

## Open questions

1. What do impostors and Nanite Voxels actually cost against each other (GPU ms, memory per species) for forests at 1-5 km, especially on hardware without Nanite (mobile, Switch-class)?
2. What overdraw and frame-time savings do alpha-to-coverage, dithered LOD crossfade and tighter leaf cards give, compared with a visibility buffer?
3. How did Ghost of Tsushima and Horizon Forbidden West implement per-blade density falloff, clumping and LOD, and what did it cost on PS4/PS5?
4. How do Virtual Shadow Maps handle cached-page invalidation when foliage moves with WPO wind, and what does that cost per frame?
5. Which Godot 4 projects show km-scale foliage (chunked MultiMesh, compute grass, visibility-range HLOD), and what frame times do they report?

## Next steps

- Watch the Ghost of Tsushima GDC talk and extract its numbers directly.
- Benchmark in your own engine. The figures here are from different hardware generations and aren't comparable as absolutes.
