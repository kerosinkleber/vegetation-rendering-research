# 07: Godot 4

[← Back to README](../README.md)

## Plugins and projects (verified from their own READMEs; no benchmarks)

| Project | What it does | Fit |
|---|---|---|
| [Terrain3D](https://github.com/TokisanGames/Terrain3D) | Terrain plus **foliage instancing with up to 10 LODs and a shadow impostor**. The foliage LODs are separate from the terrain mesh's own LODs. | Large terrains and open worlds |
| [Spatial Gardener](https://github.com/dreadpon/godot_spatial_gardener) | Paint foliage and props onto **any 3D surface**. Handles "thousands of instances without completely tanking the FPS". Godot 4.2+. | Medium-sized hand-made scenes. The author says it works best for finite scenes, so it's not meant for km-scale worlds. |
| [Octahedral Impostor LOD](https://godotengine.org/asset-library/asset/5528) (hdrsniped) | Bakes **144 views** with depth and normals. Low = 1 view, Medium = 3 blended, High = 3 blended plus depth parallax. Switches mesh ↔ impostor via **`GeometryInstance3D` visibility ranges**, with no runtime script. Forward+ and Mobile. | Distant trees. Brand new (v1.0.0). |
| [2Retr0/GodotGrass](https://github.com/2Retr0/GodotGrass) | Per-blade grass inspired by Ghost of Tsushima. Blades perpendicular to the camera are widened in view space, and blades bend by height. No wind. C#, MIT. | Reference implementation for near grass |

Also mentioned in videos: **TerraBrush** (terrain plugin with a tree system) and **Simple Grass Textured** (Icterus Games).

## Built-in Godot features to use

- **`MultiMeshInstance3D`:** GPU instancing. Split it into **spatial chunks** so each chunk can be frustum-culled. One giant MultiMesh is culled as a single object.
- **Visibility ranges** (`GeometryInstance3D.visibility_range_begin/end`, plus fade modes): manual LOD and HLOD switching, including mesh → impostor.
- **Automatic mesh LOD** at import.
- **Particle shaders (4.3+):** a GPU option for scattering grass.
- **Compute shaders (RenderingDevice):** GPU culling and blade generation if you need to go beyond MultiMesh.

## Suggested pipeline (a synthesis, not taken from any single source)

| Distance | Grass | Trees and bushes |
|---|---|---|
| Near (0-30 m) | GPU per-blade grass (GodotGrass-style), or a dense MultiMesh | Full mesh with wind shader |
| Mid (30-150 m) | Sparser, wider blades or cards; fade out with visibility range | Chunked MultiMesh, lower LOD, simple sway |
| Far (150 m+) | Terrain color or texture only | Octahedral impostors (no wind) |
| Shadows | Off beyond the near range | Terrain3D shadow impostor or the lowest LOD |

Watch **overdraw** from leaf cards most of all: the "Forest Scene" video below found it was the main foliage cost. Use Godot's overdraw debug draw mode to check.

## YouTube videos

⚠️ **Unverified:** these were found by search and have not been watched. Check upload dates and Godot versions before following one.

### Grass at scale
- [How to Render MILLIONS of Blades of Grass Efficiently in Godot](https://www.youtube.com/watch?v=z_Q0sA3wE10): several techniques and plugins. **Godot 3.x.**
- [Godot – 5 Million Individual Blades of Grass in a Field](https://www.youtube.com/watch?v=9J82NkWzAyM): a scale demo going from 100k to 5M blades.
- [How to create grass, a lot, fast, high-performance (Godot 4 3D, no talking)](https://www.youtube.com/watch?v=XL2LrofJZy4): MultiMesh grass.
- [Godot Grass shader tutorial](https://www.youtube.com/watch?v=uMB3-g8v1B0): lots of grass on a large terrain.
- [Godot GPU Instancing test with Grass](https://www.youtube.com/watch?v=VcaCyJOeAYA): about 90k blades and 260k triangles.
- [Grass Particles Shader (Godot 4)](https://www.youtube.com/watch?v=9ccn-RoumDc): uses the 4.3 particle shader to scatter grass.
- [Tutorial: Stylized Grass in Godot 4](https://www.youtube.com/watch?v=vp51qoyv08g): Blender, MultiMesh and visual shaders.
- [Stylized Grass in 5 Minutes // Godot 4 Tutorial](https://www.youtube.com/watch?v=3ftcGTp-Se8)
- [How to Create Interactive Grass in Godot](https://www.youtube.com/watch?v=H_4vSwaTpjU)
- [Simple Grass Textured preview for Godot 4](https://www.youtube.com/watch?v=E_LTrFUfrrw): a plugin.

### Trees, forests and LOD
- [Godot 4 render millions of trees MultiMeshInstance3D](https://www.youtube.com/watch?v=oZ0rxi_PFpw): GPU instancing.
- [Make a MASSIVE forest with Godot – TerraBrush DevLog](https://www.youtube.com/watch?v=iIBWgnprhYU)
- [Forest Scene – rewritten and little optimized in Godot 4](https://www.youtube.com/watch?v=8xQEjri65lo): finds overdraw is the main foliage cost.
- [Forest Scene in Godot 4 (Alpha 6) – Terrain, Placement & Assets](https://www.youtube.com/watch?v=1ho6tbxGt4c)
- [Manual LOD control on Godot 4 with GeometryInstance3D and Visibility Ranges](https://www.youtube.com/watch?v=5C0WND_c_bk)
- [Godot 4 Automatic LOD Tutorial](https://www.youtube.com/watch?v=S8dMzNRd63E)
- [I Coded A Tree Generator/LOD System In Godot](https://www.youtube.com/watch?v=x_iSVnlm3hs)
- [Godot Octahedral Impostors](https://www.youtube.com/watch?v=2O9GydimIKA): an older work in progress, about 6 years old.
- [Woods devlog #1 – Foliage scattering system in Godot 4.2](https://www.youtube.com/watch?v=SdiJStSxzw4): for a PSX-style horror game.
- [Godot: OPTIMIZED Harvestable Trees (Blender to MultiMesh & Area-Based Colliders)](https://www.youtube.com/watch?v=EiSbUe0YVvo)
- [How to Make an Open World Game Terrain in Godot 4.4](https://www.youtube.com/watch?v=JDzIAGB2jHU)
- [Comparing Game Engine Performance: Godot MultiMesh vs Unity ECS](https://www.youtube.com/watch?v=6BVoy7kOFxY)

### Background (not Godot-specific)
- [Procedural Grass in Ghost of Tsushima (GDC 2021)](https://www.youtube.com/watch?v=Ibe1JBF5i5Y): the talk GodotGrass is based on.
- Acerola's grass videos on YouTube: a common follow-up for grass techniques.
- [hexaquo.at grass rendering series](https://hexaquo.at/pages/grass-rendering-series-part-1-theory/): part 2 covers full-geometry grass in Godot.
