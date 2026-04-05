# Manga Cinematography Best Practices for Visual Novels

Extracted from kulisama81/anichat — docs/manga-cinematography-best-practices.md
Date: 2026-02-02

Reference guide for camera framing and shot selection based on manga/anime visual storytelling conventions.

## Sources

- Why Some Manga Feel Cinematic: Panel Layouts, Framing, and Visual Flow (The Manga Menagerie)
- Master Extreme Close-Up Shots: Techniques, Tips, and Famous Examples (Pixflow)
- What is the Extreme Close-Up Shot? (No Film School)
- Extreme Close-Up Shots: Creative Examples That Work (StudioBinder)
- Manga Faces: Understanding and Reading Manga Visual Language (Japan Powered)
- Close-ups: an emotive language in manga (ResearchGate)

## Core Principles

> "Close-ups remove context to trap you in a feeling. A close-up of eyes can turn a normal line into a challenge."

Cinematic manga uses layout, framing, and flow to control time and emotion. Extreme close-ups are reserved for maximum dramatic impact and symbolic significance.

## Eyes vs. Mouth: When to Use Each

### Eyes (Internal Emotions)
Use extreme close-up on EYES for:
- Internal emotions: sadness, tears, contemplation, realization, fear
- Romantic tension: love confessions, intimate moments, longing gazes
- Confrontation & challenge: intense stares, determination, eye contact as power play
- Shock & surprise: wide-open eyes, disbelief, revelation reactions

### Mouth (Action & Aggression)
Use extreme close-up on MOUTH for:
- Shouting & anger: verbal aggression, multiple exclamation marks, ALL CAPS
- Gasps & sudden reactions: "Ah!" "Oh no!"
- Physical actions: eating, drinking, biting, whispering
- Laughter (but NOT smiles): "Haha!" — genuine or manic

### CRITICAL: Never Use Mouth Close-Ups for Happy Smiles
Problem: Mouth close-ups of smiles look forced, gritted, or angry because teeth showing can look aggressive, cropping out the eyes removes warmth, and it lacks the sparkle/crescent-eye manga convention for happiness.

Solution: For happy/positive messages use regular close-up (full face), eyes close-up (crescent/closed eyes), or mid-shot.

## Camera Angle Decision Matrix

| Message Type | Emotion | Recommended Angle | Reason |
|---|---|---|---|
| "I'm so sad..." | Sad | Eyes close-up | Internal emotion, tears |
| "What!?" | Surprised | Eyes close-up | Wide-eyed shock |
| "Shut up!" | Angry | Mouth close-up | Shouting aggression |
| "I love you" | Romantic | Eyes close-up | Intimate, vulnerable |
| "Haha!" | Happy (laughing) | Mouth close-up | Action of laughing |
| "I'm doing good!" | Happy | Close-up (full face) | Avoid mouth — smiles look forced |
| "NO!!!" (caps) | Angry/Desperate | Mouth close-up | Extreme verbal emphasis |
| "..." (thinking) | Neutral | Eyes close-up | Contemplation |

## Manga Emotional Expression Guide

### Anger
- Eyebrows lowered and slanted toward center, mouth open (shouting), triangle eyes
- Shouting anger: mouth close-up. Internal anger: eyes close-up. Confrontational: full face.

### Happiness
- Upward curving mouth, crescent-shaped eyes, sparkle in eyes
- Soft happiness: close-up with eyes + smile. Energetic: mid-shot. Avoid mouth close-up.

### Sadness
- Tears glistening, downcast gaze, trembling lips, lack of eye shine
- Primary: eyes close-up. Avoid mouth close-up.

### Surprise
- Wide open eyes, visible whites around iris, mouth open in O-shape
- Default: eyes close-up. Verbal surprise: mouth close-up if gasp/exclamation.

## Cinematic Flow in Manga

Panel progression for emotional build:
1. Wide shot: establish scene
2. Mid-shot: characters interact, dialogue begins
3. Close-up: emotion builds, focus on reaction
4. Extreme close-up: peak emotional moment
5. Cut away: release tension, return to wider shot

## Implementation in AniChat

Sentiment analyzer system with priority order:
1. Sentiment analysis (highest confidence) — analyzes message text patterns
2. Emotion detection — AI-detected emotion as fallback
3. Conversation context — dialogue exchanges, message length
4. Variety enforcement — prevents repetitive angles

Pattern examples for eyes focus: realization, sadness, romantic, shock keywords.
Pattern examples for mouth focus: shouting (multiple ! or CAPS), anger verbal, eating, gasp.
Avoid mouth patterns: happy_smile, positive_calm.
