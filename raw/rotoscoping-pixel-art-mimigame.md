# Rotoscoping for Pixel Art Game Sprites

Combined from kulisama81/Mimigame — README.md + Claude.md
Date: 2026-01-18

## Overview

Mimigame is a Prince of Persia style game featuring Mimi the Great Pyrenees, with hand-crafted pixel art sprites created using authentic rotoscoping techniques — the same method Jordan Mechner used in the original Prince of Persia (1989).

The project transforms real video footage of Mimi running in the snow into pixel art sprites suitable for a retro-style platformer game.

## Rotoscoping Technique

### Historical Context
Jordan Mechner filmed his brother running and fighting, then traced the footage frame by frame to create the remarkably fluid animations in Prince of Persia (1989). This rotoscoping technique produces natural, lifelike motion that hand-drawn keyframe animation can't match.

### Modern Application
1. Extract frames from real video (171 frames of Mimi running)
2. Select key poses — every ~10th frame works well
3. Process to 80x80px reference images (prepare_references.py)
4. Enhance contrast and sharpness
5. Create pixel art sprites using the references

### 9-Frame Running Cycle (Complete Dog Gallop)
1. Contact — front paw touches ground
2. Recoil — body compresses, absorbing weight
3. Passing — body rises over support leg
4. Lift — pushing off, extending
5. Airborne — all paws off ground, fully extended
6. Extended — peak of flight
7. Landing — legs reaching for ground
8. Contact Rear — rear paw touches down
9. Push Cycle — completing the loop

## Prince of Persia Style Rules

1. Bold outlines: 2-4px thick for readability
2. Limited palette: 11 colors maximum (8-12 range)
3. Geometric shapes: simplified, not photorealistic
4. Clear silhouettes: readable at any size
5. Seamless loops: last frame must flow to first frame
6. Rotoscoped motion: based on real footage

## Sprite Specifications

| Property | Value |
|---|---|
| Frame Size | 80x80 pixels |
| Total Frames | 9 (complete run cycle) |
| Format | PNG with transparency (RGBA) |
| Sprite Sheet | Horizontal strip, 2px padding between frames |
| Recommended FPS | 10-12 (authentic retro feel) |
| Color Palette | 11 colors |
| File Size | ~3KB (sprite sheet), <1KB per frame |

## Technical Stack
- LibreSprite 1.2-dev (compiled from source — Homebrew cask not available)
- Python 3 + PIL/Pillow for image processing and automation
- HTML5 Canvas for interactive demo
- Pygame for desktop demo

### LibreSprite Gotchas
- V8 scripting doesn't work (embedder-vs-build mismatch) — GUI only
- Tablet support not available, use mouse
- Known harmless warnings about unknown events (10, 12) on macOS

## Critical Rules
- NEVER use image smoothing — pixel art must stay crisp (ctx.imageSmoothingEnabled = false)
- NEVER rescale sprites with smoothing — always nearest-neighbor (PIL: Image.NEAREST)
- NEVER add anti-aliasing — hard edges only
- Keep animation at 10-12 FPS for sprites (separate from 60 FPS rendering loop)

## Performance
- Sprite sheet: ~3KB
- Memory: ~230KB for all 9 frames loaded (80x80x4 bytes x 9)
- Render: negligible impact at 60 FPS
- Pre-load all sprite images before starting animation

## Planned Animations
- Idle/Standing (4-6 frames, breathing animation)
- Walking (8 frames, slower pace)
- Jumping (6-8 frames, leap cycle)
- Turning (4-6 frames)
- Sitting (4-6 frames)

## Credits
Subject: Mimi the Great Pyrenees
Inspiration: Prince of Persia (1989) by Jordan Mechner
Technique: Rotoscoping
