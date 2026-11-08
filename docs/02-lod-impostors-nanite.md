# 02: LOD: impostors, billboards, HLOD and Nanite

[← Back to README](../README.md)

## Octahedral / hemisphere impostors

An impostor is a single card that shows the tree from a set of pre-baked view angles. Octahedral mapping spreads those angles evenly over a sphere or hemisphere.

### Fortnite Battle Royale (Epic Impostor Baker): **High**

- **"Upper Hemisphere Imposters for all of our trees when Nanite is disabled."** (Epic's wording.)
- Bake: a **12×12 frame grid = 144 frames** in a **2048 atlas**.
- Runtime: the material picks the **3 nearest captured frames** and blends them on one card. Mobile uses a single frame sample.
- What Fortnite does on platforms with Nanite isn't stated. Nanite presumably replaces impostors there, but that is an inference.

### Impostor vs billboard: **Medium**

| | Triangles | Vertices |
|---|---|---|
| Impostor (1 card) | 8 | 9 |
| 8-card billboard | 72 | 81 |

Epic says impostors are "generally much faster to render" despite the more expensive pixel shader from blending 3 frames, and that billboards overdraw worse because of their masked materials. There are no millisecond numbers, and the comparison is only against Epic's own billboard setup.

Source: Epic, *Impostor Baker Plugin* docs (UE 5.8).

Background reading (blog, not verified claim-by-claim): Ryan Brucks, [Octahedral Impostors](https://shaderbits.com/blog/octahedral-impostors).

## UE5 World Partition HLOD: **Medium**

- Epic calls the **Instancing** HLOD layer type ideal for impostor meshes such as trees and foliage. It replaces Static Meshes with Instanced Static Mesh components at each asset's lowest LOD instead of merging them.
- ❌ **Refuted (1-2):** "HLOD generates one merged proxy mesh per layer." That is not how the Instancing layer works.

## UE5 Nanite Foliage (experimental)

### Nanite Voxels (UE 5.7+): **Medium**

- Once clusters get small enough, Nanite switches them from triangles to **near-pixel-sized aggregate voxels**, which are rasterized separately and "generally" perform better than triangles at distance.
- As distance grows, the voxels get fewer and larger, keeping roughly 1 pixel on screen.
- Before 5.7, Nanite struggled with thin, clustered geometry and masked materials, and large forests had performance problems.

### Nanite Assemblies: **Medium**

- Instances repeated parts such as branches inside one asset, up to 65k instances per assembly.
- Largest Witcher 4 demo tree: **3.5 GB → ~29 MB** on disk, and streaming memory in one view **~36 MB → ~2.7 MB**.

### Nanite Skinning (wind): see [04](04-wind-animation.md).

All Nanite numbers are Epic's own measurements on its own demo content. No independent benchmark exists, and **no source compares impostors with Nanite Voxels at 1-5 km.**

Sources: Epic *Nanite Foliage* docs; UE 5.7 release notes; NoobFeed (secondary).

## Takeaways

- Without Nanite, octahedral impostors at 144 frames with a 3-frame blend are the industry-proven default for distant trees.
- With Nanite on UE 5.7+, voxels replace both billboards and LOD chains, but the feature is still experimental.
- Instancing-style HLOD fits foliage better than merged proxy meshes.
