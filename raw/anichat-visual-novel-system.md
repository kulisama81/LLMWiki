# AniChat Visual Novel System

Combined from kulisama81/anichat — EMOTIONS_IMPLEMENTATION_SUMMARY.md + VISUAL-NOVEL-IMPLEMENTATION.md
Date: 2026-02-02

## Part 1: Emotion Detection System

### Architecture
Detects 6 emotions: happy, sad, angry, surprised, romantic, neutral.
94% accuracy on test suite (17/18 correct). <10ms detection time (~2ms actual).

### API
- EmotionDetector.detectEmotion(text) — returns emotion string
- EmotionDetector.getConfidenceScore(text, emotion) — returns 0-100
- EmotionDetector.getEmotionScores(text) — returns all scores

### Approach
Keyword-based analysis with emoji support and punctuation pattern analysis. Not ML-based — lightweight NLP with three-tier fallback rendering pipeline.

### Three-Tier Avatar Rendering
- Tier 1: Pre-generated emotion avatar (instant, from assets/avatars/pregenerated/)
- Tier 2: Dynamic text-integrated cache (future, LRU with 50 entries, ~90MB)
- Tier 3: HTML fallback (CSS speech bubble, always works)

### Known Limitations
- No compound emotion detection
- Sarcasm not detected (requires NLP, out of scope)
- 80 char limit for text in prompts
- No persistent cache (resets on page reload)

### Notable Bug Fix
"I'm good!" was detected as angry because exclamation marks incorrectly boosted anger scores for positive messages. The fix: avoid mouth patterns for happy_smile and positive_calm keywords.

## Part 2: Visual Novel Camera Director

### 4 Camera Angles
- Close-up: Face fills 90% of frame (emotional intensity)
- Mid-shot: Head and shoulders, 60% of frame (standard dialogue)
- Wide-shot: Full body, environmental context (long messages 150+ chars)
- Over-shoulder: Back of one character, other in focus (conversation reply)

### Intelligent Camera Selection
Rules based on emotion + message length + conversation context:

| Emotion | Length | Context | Camera |
|---|---|---|---|
| Angry/Sad/Romantic | <50 chars | Any | Close-Up |
| Happy/Surprised | <50 chars | Any | Mid-Shot |
| Any | <100 chars | Reply | Over-Shoulder |
| Any | >150 chars | Descriptive | Wide-Shot |
| Default | 50-150 chars | Normal | Mid-Shot |

### Variety Enforcement
Tracks last 3 camera angles. Forces variation if same angle used 3+ times.

### Scene Composition (Multi-Layer)
z-index 3: Speech Bubble (overlay)
z-index 2: Speaker Character (foreground)
z-index 1: Listener Character (background, for over-shoulder)
z-index 0: Scene Background (optional, for wide-shot)

### Visual Novel CSS
Full-height scenes (80vh desktop, 70vh tablet, 60vh mobile). Lichtenstein art style with Ben-Day dots, bold outlines, primary colors. Speech bubbles with Comic Sans, uppercase text, border-radius 20px.

### Animations
- vnFadeIn: Default entrance (0.5s)
- vnFadeZoom: Close-up emphasis (0.6s)
- vnSlideUp: Wide-shot entrance (0.5s)
- bubblePop: Speech bubble appearance (0.3s)

### Total Implementation
~1,140 lines of code across 3 new files (camera-director.js 220 lines, scene-composer.js 320 lines, visual-novel.css 450 lines) and 5 modified files.

### Design Inspiration
Tales of Arise bonding camp scenes + Roy Lichtenstein pop art (1960s).
