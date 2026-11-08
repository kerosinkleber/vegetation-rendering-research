# Vegetation Rendering Research

How games render grass, shrubs, bushes and trees **fast** and **far**. The research is compiled from GDC/SIGGRAPH talks, engine docs and developer write-ups, with a dedicated section on **Godot 4**.

Most findings here went through a multi-agent research pass. Sources were fetched, claims extracted, and each claim voted on by three skeptical reviewers (a claim needed 2 of 3 refutations to be rejected). Each claim is tagged with a confidence level:

| Tag | Meaning |
|---|---|
| **High** | Primary source (talk, paper, official docs), confirmed 3-0 |
| **Medium** | A single primary source, vendor-only numbers, or a secondary source |
| **Unverified** | Found by search but not checked (mostly the YouTube list) |

## The short version

Shipped games make dense vegetation affordable through five habits:

1. **The GPU decides what to draw.** Compute shaders cull instances and small clusters (frustum, Hi-Z occlusion, backface) and submit the survivors through a few indirect draws. → [01](docs/01-gpu-driven-culling.md)
2. **Generate placement instead of storing it.** Density maps plus GPU rules, and grass blades generated per frame. → [03](docs/03-grass-and-procedural-placement.md)
3. **Make the representation cheaper with distance.** Mesh LODs → impostors or voxels, and HLOD for whole regions. → [02](docs/02-lod-impostors-nanite.md)
4. **Make the animation cheaper with distance.** One wind vector per instance, simpler shader variants far away, no animation below a screen-size threshold. → [04](docs/04-wind-animation.md)
5. **Fight overdraw, the real cost in forests.** Trim leaf cards, depth pre-pass, visibility buffer, LOD scale. → [05](docs/05-overdraw-and-alpha.md)

Shadows for distant foliage are updated over several frames instead of every frame. → [06](docs/06-shadows.md)

## Contents

| # | Topic |
|---|---|
| [01](docs/01-gpu-driven-culling.md) | GPU-driven culling, instancing and indirect draws |
| [02](docs/02-lod-impostors-nanite.md) | LOD: impostors, billboards, HLOD, Nanite Foliage and Voxels |
| [03](docs/03-grass-and-procedural-placement.md) | Grass and procedural placement (Horizon, Ghost of Tsushima/Yōtei) |
| [04](docs/04-wind-animation.md) | Cheap wind animation (Crysis, SpeedTree, Pivot Painter, Nanite Skinning) |
| [05](docs/05-overdraw-and-alpha.md) | Overdraw and alpha-tested foliage |
| [06](docs/06-shadows.md) | Shadows for distant foliage |
| [07](docs/07-godot.md) | **Godot 4**: plugins, a suggested pipeline, YouTube videos |
| [08](docs/08-gaps-and-open-questions.md) | What this research could not verify |
| — | [SOURCES.md](SOURCES.md): all sources with quality ratings |

## Headline numbers

| Technique | Number | Source |
|---|---|---|
| GPU-driven cluster rendering (AC Unity) | 10-100× fewer draw calls; ~10× objects at ~75% CPU cost | SIGGRAPH 2015 |
| Two-phase Hi-Z culling (RedLynx) | 250k objects in 2.3 ms GPU / 0.2 ms CPU (Xbox One) | SIGGRAPH 2015 |
| Procedural placement (Horizon Zero Dawn) | 100k+ objects at ~250 µs average GPU; ~4 MB/km² of maps | GDC 2017 |
| GPU culling (Ghost of Yōtei) | >1M trees, rocks and bushes → ~60k drawn | PlayStation Blog 2025 |
| Nanite Assemblies (Witcher 4 demo tree) | 3.5 GB → 29 MB on disk | Epic docs |
| Nanite Skinning wind | ~0.1 ms per 100k bones | Epic docs |
| Impostor vs 8-card billboard | 8 tris vs 72 tris | Epic docs |

## Caveats

- Much of the hard data is old: Crysis is from 2007, AC Unity and RedLynx from 2015, and the console numbers are Xbox One/PS4. Use the numbers as relative scale, not as current-gen budgets.
- Most culling and shadow results cover general scene geometry, not vegetation specifically.
- Nanite figures are Epic's own measurements on its own content, and the feature is experimental.
- The research date is November 2026.
