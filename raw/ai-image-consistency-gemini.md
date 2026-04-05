# AI Image Generation Consistency with Gemini (Nano Banana)

Extracted from kulisama81/anichat — docs/nano-banana-consistency-guide.md
Date: 2026-02-02

Guide for achieving consistent character generation with Google's Nano Banana (Gemini 2.5 Flash Image) model.

## Sources
- Nano Banana Prompt Guide (Leonardo.Ai)
- Generate Images Using Nano Banana Pro Model (Leonardo.Ai docs)
- Ultimate Nano Banana Prompt Guide (GLBGPT)

## Key Principles

### 1. Extremely Detailed Character Descriptions
The more specific details you provide, the more consistent the output. Not "blonde hair" but "shoulder-length golden blonde hair with soft waves, side-swept bangs partially covering right eyebrow."

Key details to always specify:
- Exact hair color, length, and style
- Precise clothing items with colors
- Specific facial features (eye color, face shape, nose size)
- Accessories and consistent elements
- Explicit instruction that appearance must remain constant

### 2. 360-Degree Character Sheet Approach
Build complete character reference through multi-angle generation: front, left, right, back. In AniChat: 6 emotion variants + back-view, all sharing identical character description prompts.

### 3. Structured Prompt Format
Optimal structure: [STYLE] -> [FRAMING] -> [CHARACTER] -> [EMOTION] -> [BACKGROUND] -> [CONSTRAINTS]

### 4. Consistency Through Explicit Constraints
"CRITICAL: Same exact outfit, hairstyle, and features in ALL emotions — only facial expression changes"

Key constraint phrases that work:
- "CRITICAL: Same exact outfit in ALL emotions"
- "IMPORTANT: Only facial expression changes"
- "Must match front-view character details exactly"

### 5. Separation of Static and Variable Elements
Character description (static) stays identical. Only emotion description (variable) changes per generation. Never include outfit or hairstyle details in emotion descriptions.

### 6. Caching Strategy
LRU cache with 50 entries. Cache key = character-emotion-cameraAngle. Reduces API calls and ensures exact consistency for repeated requests.

### 7. Model Differences
- Nano Banana (Gemini 2.5 Flash): Fast, good for character portraits, consistent character sheets
- Nano Banana Pro (Gemini 3 Pro): Complex scenes, data-heavy infographics, text rendering

For AniChat: Standard Nano Banana is optimal — need speed and consistency, not complex scenes.

## Common Pitfalls
1. Vague character descriptions
2. Mixing character and emotion details
3. Forgetting explicit constraints
4. Inconsistent terminology across prompts
5. Not pre-generating assets (batch generate all variants with identical base prompts)

## Real-World Implementation (AniChat)
- 12 emotion avatars (2 characters x 6 emotions) with consistent appearance
- 2 back-view portraits matching front-view characters
- Only facial expressions differ across emotions
- Camera angles maintain character consistency via separate framing instructions
- Emotion-specific backgrounds that don't affect character appearance
