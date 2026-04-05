---
title: AniChat Visual Novel System
type: entity
sources: [anichat-visual-novel-system.md, manga-cinematography-best-practices.md, ai-image-consistency-gemini.md]
created: 2026-04-04
updated: 2026-04-04
---

# AniChat Visual Novel System

AniChat is a visual novel chat app built in one day (2026-02-03) with 100% Claude commits. It combines [[sentiment-analysis]], [[source-manga-cinematography]], and [[source-ai-image-consistency]] into an interactive storytelling system inspired by Tales of Arise bonding scenes and Roy Lichtenstein pop art.

## Architecture

Three interlocking systems:

### 1. Emotion Detection (emotion-detector.js)
Keyword + emoji + punctuation analysis detecting 6 emotions: happy, sad, angry, surprised, romantic, neutral. 94% accuracy, <2ms detection time. Not ML-based — lightweight pattern matching with documented edge cases (the "I'm good!" anger bug).

### 2. Camera Director (camera-director.js)
Selects camera angles based on emotion, message length, and conversation context:
- Angry/sad/romantic + short message -> close-up
- Reply to previous speaker -> over-shoulder
- Long message (150+ chars) -> wide-shot
- Default -> mid-shot

Enforces variety by tracking last 3 angles and forcing changes after repetition.

### 3. Scene Composer (scene-composer.js)
Composites multi-layer visual novel scenes with z-indexed elements: background, listener character, speaker character, speech bubble. Full-height scenes (80vh) with Lichtenstein styling.

## Image Generation

Uses Google Gemini 2.5 Flash (Nano Banana) with structured prompts that separate static character descriptions from variable emotion/framing. Pre-generates 12 emotion avatars + 2 back-view portraits. LRU cache prevents redundant API calls.

## Notable Design Decisions

- **Lichtenstein art style**: Ben-Day dots, bold outlines, primary colors, Comic Sans uppercase text
- **Three-tier fallback**: pre-generated PNG -> talking/idle PNG -> SVG fallback
- **Total implementation**: ~1,140 lines across 3 new files + 5 modified files
- **Single-day build**: demonstrates Claude's rapid prototyping capability

See also: [[source-manga-cinematography]], [[source-ai-image-consistency]], [[source-claude-usage-patterns]]
