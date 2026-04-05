---
title: "Source Summary: Rotoscoping for Pixel Art"
type: source-summary
sources: [rotoscoping-pixel-art-mimigame.md]
created: 2026-04-04
updated: 2026-04-04
---

# Rotoscoping for Pixel Art Game Sprites

Summary of how Mimigame recreates Jordan Mechner's Prince of Persia (1989) rotoscoping technique with modern tools to animate a Great Pyrenees dog.

## Key Takeaway

Rotoscoping — tracing real video footage frame by frame — produces fluid, lifelike motion that hand-drawn keyframe animation can't match. Mechner filmed his brother; this project filmed a dog running in snow. The technique translates directly from 1989 to 2026 with modern tools (PIL/Pillow instead of hand tracing, LibreSprite instead of Deluxe Paint).

## The Pipeline

1. Record real video (171 frames of Mimi running)
2. Select key poses (~every 10th frame, 9 total for complete gallop)
3. Process to 80x80px reference images
4. Create pixel art sprites following PoP style rules
5. Assemble into seamless loop sprite sheet

## 9-Frame Dog Gallop Cycle

Contact -> Recoil -> Passing -> Lift -> Airborne -> Extended -> Landing -> Contact Rear -> Push. Each frame must flow seamlessly back to frame 1.

## Prince of Persia Style Rules

- Bold outlines (2-4px), limited palette (11 colors max), geometric not photorealistic, clear silhouettes at any size
- 80x80px frames at 10-12 FPS (separate from 60 FPS render loop)
- NEVER use image smoothing — always nearest-neighbor scaling
- Sprite sheet: ~3KB total, <1KB per frame

## Critical Gotcha

LibreSprite 1.2-dev V8 scripting is broken (embedder-vs-build mismatch) — GUI works perfectly but scripting does not. Use PIL/Pillow for automation instead.

See also: [[source-claude-usage-patterns]]
