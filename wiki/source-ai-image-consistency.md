---
title: "Source Summary: AI Image Consistency with Gemini"
type: source-summary
sources: [ai-image-consistency-gemini.md]
created: 2026-04-04
updated: 2026-04-04
---

# AI Image Consistency with Gemini (Nano Banana)

Summary of techniques for achieving consistent character appearance across multiple AI-generated images using Google's Gemini 2.5 Flash (Nano Banana) model.

## Key Takeaway

Consistency in AI image generation is achieved through **extreme specificity** and **explicit constraints**, not through model fine-tuning. The approach separates static character details from variable emotion expressions and enforces identity with constraint phrases like "CRITICAL: Same exact outfit in ALL emotions."

## Five Pillars of Consistency

1. **Extremely detailed descriptions** — not "blonde hair" but "shoulder-length golden blonde hair with soft waves, side-swept bangs partially covering right eyebrow"
2. **Explicit constraints** — "CRITICAL: Same exact outfit, hairstyle, and features in ALL emotions"
3. **Separation of concerns** — character description (static) vs. emotion description (variable) as separate prompt sections
4. **Identical base prompts** — copy-paste identical character block across all generations
5. **Caching** — LRU cache with 50 entries ensures exact reuse of generated images

## Structured Prompt Format

`[STYLE] -> [FRAMING] -> [CHARACTER] -> [EMOTION] -> [BACKGROUND] -> [CONSTRAINTS]`

Character block stays frozen. Only framing, emotion, and background change.

## Model Choice

Nano Banana (Gemini 2.5 Flash) for speed and consistency in character portraits. Nano Banana Pro (Gemini 3 Pro) for complex scenes but overkill for simple portraits.

## Results in [[anichat]]

12 emotion avatars (2 characters x 6 emotions) + 2 back-view portraits, all with consistent appearance. Only facial expressions differ.

See also: [[source-manga-cinematography]], [[anichat-visual-novel-system]]
