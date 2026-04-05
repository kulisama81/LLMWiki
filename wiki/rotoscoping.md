---
title: Rotoscoping
type: concept
sources: [rotoscoping-pixel-art-mimigame.md]
created: 2026-04-04
updated: 2026-04-04
---

# Rotoscoping

Animation technique of tracing real video footage frame by frame to produce fluid, lifelike motion.

## History

Pioneered by Jordan Mechner for Prince of Persia (1989) — he filmed his brother running and fighting, then traced the footage to create the game's remarkably natural animations. The technique produces motion quality that hand-drawn keyframe animation can't match because it captures real biomechanics.

## Modern Application (Mimigame)

The [[source-rotoscoping-pixel-art]] project applies the same technique with modern tools:
- Video capture -> PIL/Pillow processing (instead of hand tracing)
- LibreSprite for pixel art editing (instead of Deluxe Paint)
- 171 frames of a Great Pyrenees running -> 9 key poses selected -> pixel art sprites

## Key Frame Selection for a Dog Gallop

9 poses form a complete cycle: Contact, Recoil, Passing, Lift, Airborne, Extended, Landing, Contact Rear, Push. Every ~10th frame from source video works well for key pose selection.

## Prince of Persia Style Constraints

- 80x80px frames, 10-12 FPS
- Bold outlines (2-4px), limited palette (11 colors max)
- Geometric simplification, not photorealistic tracing
- Clear silhouettes readable at 1x size
- Seamless loop (frame 9 flows to frame 1)
- NEVER use image smoothing — nearest-neighbor only

See also: [[source-rotoscoping-pixel-art]]
