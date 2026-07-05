# EduVerse — Confidence Mode (Desktop / Teacher Host View) UI Prompt

## Reference Image
`1_lobby.png`

---

## Visual Design Prompt

Build the **Confidence Mode** game host screen. Full-screen game view.

### Screen: Lobby / Waiting for Players

**Background**
- Full-screen very dark near-black background.
- Left and right edges: same Egyptian stone-column/ruins motif as Accuracy Mode, but with **purple** flaming torches instead of blue.
- Floor: **purple/violet** glowing holographic ring radiating from center-bottom.
- Overall color palette: deep purple, violet, magenta-purple neon accents — a mystical, mind-themed aesthetic.
- Faint purple particle stars scattered across the background.

**Header / Title Banner**
- Centered at top: rectangular neon-purple bordered banner.
- Above banner: glowing 3D brain icon with a purple shield/checkmark badge — conveying "confident knowledge."
- Inside banner: "**CONFIDENCE MODE**" in large bold white text with purple glow.
- Below: "— WAITING FOR PLAYERS TO JOIN —" in small purple caps.

**Left Panel — Join Info**
- Dark panel with purple/violet border.
- "**SCAN TO JOIN**" label.
- QR Code (white/black).
- "OR" divider.
- "**GAME PIN**" label with large bold PIN number in purple accent.

**Center Panel — Players Joined**
- Large dark area with purple border — player tiles appear as students join.

**Exit Button**
- Top-right: back arrow icon in dark hexagonal frame.

---

## Game Flow (Confidence Mode)

```
Teacher selects Confidence Mode
        ↓
System generates Game PIN + QR Code
        ↓
Lobby — students join
        ↓
Teacher starts game
        ↓
Question appears on teacher screen + sent to student devices
        ↓
Students:
  1. Select their answer (A / B / C / D or True/False)
  2. THEN select their Confidence Level:
     - Level 1 (Low Confidence)  → 1 smiley face
     - Level 2 (Medium Confidence) → 2 smiley faces
     - Level 3 (High Confidence)   → 3 smiley faces
        ↓
Answer + Confidence Level submitted
        ↓
Answer revealed on teacher screen
        ↓
Score calculated (answer × confidence multiplier)
        ↓
Next question → Repeat
        ↓
Final Leaderboard
```

---

## Confidence Mode Rules
- Individual player mode.
- Students must select BOTH an answer AND a confidence level.
- **Scoring matrix:**

| Result | Confidence 1 | Confidence 2 | Confidence 3 |
|--------|--------------|--------------|--------------|
| Correct | +100 pts | +200 pts | +300 pts |
| Wrong | -50 pts | -100 pts | -150 pts |

- High confidence + correct = maximum reward.
- High confidence + wrong = maximum penalty.
- Promotes metacognition — students learn to evaluate their own certainty.
- Teacher may optionally enable a per-question timer.

---

## Frontend Architecture

```
ConfidenceModeHostView
  ├── FullscreenBackground (purple stone walls + torch flames)
  ├── BrainShieldIcon (top center, glowing purple)
  ├── TitleBanner ("CONFIDENCE MODE")
  ├── LeftPanel (QR + PIN)
  ├── PlayersPanel (real-time grid)
  ├── AnswerProgressDisplay (X of Y have answered + confidence chosen)
  ├── StartGameButton
  └── ExitButton
```

### Real-Time State (WebSocket)
```js
{
  gamePin: string,
  players: Player[],
  gameStatus: "lobby" | "question" | "reveal" | "leaderboard",
  currentQuestion: Question | null,
  responsesSummary: {
    answered: number,
    confidenceLevelDistribution: { 1: number, 2: number, 3: number }
  }
}
```

---

## Backend Architecture

### API Endpoints
```
POST /api/games/confidence/create
Body: { quizId: string, timeLimitPerQuestion?: number }
Response: { gamePin: string, gameId: string }

WS /ws/games/:gameId
→ Events: player_joined, answer_with_confidence_submitted
← Events: question_start, answer_reveal, leaderboard_update, game_over
```

### Scoring Logic (Server-side)
```
confidence_multiplier = { 1: 1, 2: 2, 3: 3 }
correct_base = 100
wrong_base = -50

if correct:
  points = correct_base * confidence_multiplier[confidence_level]
else:
  points = wrong_base * confidence_multiplier[confidence_level]

// Clamp minimum total score at 0 (no negative total allowed)
player.totalScore = Math.max(0, player.totalScore + points)
```

### database_additions
```sql
-- extend game_answers table
ALTER TABLE game_answers ADD COLUMN confidence_level INTEGER; -- 1, 2, or 3
```

---

## Sound & Audio (Confidence Mode)

| Sound Slot | Trigger | Default Sound |
|------------|---------|---------------|
| Lobby Music | Waiting for players | Mystical ambient loop |
| Player Join | New player joins | Soft chime |
| High Confidence Correct | Level 3 + correct | Epic triumphant burst |
| High Confidence Wrong | Level 3 + wrong | Dramatic failure horn |
| Low Confidence Correct | Level 1 + correct | Soft ding |
| Low Confidence Wrong | Level 1 + wrong | Soft buzz |
| Answer Reveal | All answers shown | Suspenseful whoosh |
| Leaderboard | Final standings | Victory fanfare |

### Assigning Sounds (Settings → Sound → Confidence Mode)
Upload `.mp3`, `.wav`, or `.ogg` for each slot. Factory defaults used if empty.
