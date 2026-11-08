# 05: Overdraw and alpha-tested foliage

[← Back to README](../README.md)

In dense forests, the GPU spends most of its time shading the same pixels many times over, through layered alpha-tested leaf cards. Triangle count matters less.

## SpeedTree guidance: **High** (qualitative)

- The **global LOD scalar** is perhaps the single biggest frame-rate lever.
- **Draw distance** sharply raises billboard and instance-buffer cost.
- Overdraw mitigations:
  - Trim interior leaf triangles.
  - Keep leaf shaders minimal.
  - Render front to back.
  - Use a **depth pre-pass**, which helps especially with cascaded shadow maps.
- SpeedTree's own words: "For dense forests, overdraw can get out of hand quickly."

Source: SpeedTree Runtime SDK, *GPU Performance*.

## Crysis: **High**

Grass was shaded **entirely per-vertex** because of its fill-rate cost. Other vegetation used a mix of per-pixel and per-vertex shading. (2007.)

## Horizon Forbidden West: visibility buffer: **High**

- A **loosely tiled deferred texturing** system speeds up foliage and alpha-tested geometry.
- A **visibility buffer** is drawn as a pre-pass, then analysis and shading run **entirely in compute shaders**.
- Running in compute allows **software variable-rate shading** and overlaps with G-buffer and shadow rendering on the PS4/PS5 compute pipes.
- No performance numbers are public.

Source: James McLaren, *Adventures with Deferred Texturing in Horizon Forbidden West*, GDC 2022.

## Alan Wake 2: **High**

Alpha-tested geometry is its own bucket in the generic GPU pipeline, and culling handles occlusion from alpha-tested geometry. See [01](01-gpu-driven-culling.md).

## Impostors vs billboards

Billboards overdraw worse because of their masked materials. See [02](02-lod-impostors-nanite.md).

## ❌ Refuted

> "A full stack of foliage techniques cuts effective overdraw from 8-15× to 1-2× and foliage frame cost by 4-6×."

Voted down **0-3**. A blog presented these aggregate figures with no measurement source.

## Not verified (gaps)

There are no measured numbers for alpha-to-coverage, dithered or temporal LOD crossfade, tighter leaf-card outlines, or mesh-shader foliage cards. See [08](08-gaps-and-open-questions.md).

## Takeaways

- Profile overdraw first. It's usually the bottleneck.
- Use a depth pre-pass for alpha-tested foliage so the expensive shader runs once per pixel.
- Fit leaf cards tightly around the leaves and keep leaf shaders cheap.
- A visibility buffer (shading decoupled from rasterization) is the modern AAA answer.
