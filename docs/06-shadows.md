# 06: Shadows for distant foliage

[← Back to README](../README.md)

## Cyberpunk 2077 (CD Projekt Red): **Medium** for foliage, **High** for the system itself

- Up to **4 cascaded shadow maps rendered every frame** for nearby and dynamic objects.
- Plus **4 "distant shadow" cascades**, each **4× larger** than the previous. The last one covers the **whole 256 km² map**.
- Distant cascades are drawn **under a per-frame budget spread over several frames** and refresh every few seconds, an approach that follows CryEngine (Kasyan 2013).
- The talk covers all meshes, not foliage specifically, so applying it to vegetation is an inference.

Source: Dybisz & Witanowski, *Shadows Optimizations in Cyberpunk 2077*, SIGGRAPH 2021 Talks.

## Other data points

- **AC Unity:** GPU cluster culling removed **30-80% of shadow triangles**. **High.**
- **Saber Engine:** frustum-only culling for shadow cascades, with no occlusion culling. **High.**
- **Terrain3D (Godot):** foliage has a dedicated **shadow impostor**. See [07](07-godot.md).

## Not verified

Virtual Shadow Maps or other cached shadows with **animated** foliage. The concern is that WPO wind invalidates cached pages every frame. Only forum threads were found. See [08](08-gaps-and-open-questions.md).

## Takeaways

- Split shadows into near shadows rendered every frame and far shadows refreshed over several frames.
- Cull shadow casters on the GPU at cluster level, where the savings are biggest.
- Use cheap shadow proxies (impostors) for distant foliage, and stop animating distant casters so cached shadows stay valid.
