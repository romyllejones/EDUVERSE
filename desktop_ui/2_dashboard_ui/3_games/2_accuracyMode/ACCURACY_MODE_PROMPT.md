# EduVerse — Accuracy Mode (Desktop / Teacher Host View) UI Prompt

## Reference Image
`1_waiting_for_players.png`

---

## Visual Design Prompt

Build the **Accuracy Mode** game host screen for the teacher's desktop display. Full-screen game view.

### Screen: Lobby / Waiting for Players

**Background**
- Full-screen very dark near-black background with subtle Egyptian stone-wall / ancient-ruins texture on left and right edges (carved stone columns, hieroglyphic-style wall carvings).
- Two flaming blue torches mounted on the stone walls on left and right.
- The floor has a neon-**teal/cyan** glowing circular holographic ring platform radiating from center-bottom.
- Overall color palette: dark charcoal, deep teal, and cyan neon accents (contrast to Classic Mode's pure blue).

**Header / Title Banner**
- Centered at top: tall rectangular neon-teal bordered banner with tech corner decorations.
- Above banner: a glowing target/bullseye icon with an arrow and a green checkmark badge — rendered in teal neon.
- Inside banner: "**ACCURACY MODE**" in large white bold uppercase text with cyan/teal glow.
- Below (inside banner): "— WAITING FOR PLAYERS TO JOIN —" in small teal caps.

**Left Panel — Join Info**
- Dark panel with teal/cyan border.
- "**SCAN TO JOIN**" label.
- QR Code (white background, black QR pattern).
- "OR" divider.
- "**GAME PIN**" label.
- Large PIN number (e.g., `482731`) in bold white text on dark inset background.

**Center Panel — Players Joined**
- Large dark area with teal border.
- Player tiles appear in real-time as students join.

**Exit Button**
- Top-right: small icon button (back/exit arrow in a dark hexagonal frame) to return to dashboard.

---

## Game Flow (Accuracy Mode)

```
Teacher selects Accuracy Mode
        ↓
System generates Game PIN + QR Code
        ↓
Lobby displayed — students join
        ↓
Teacher starts game
        ↓
Question displayed (no timer or extended timer) — students answer on devices
        ↓
After ALL students answer (or time limit) → Answer revealed
        ↓
Score is based purely on CORRECTNESS — NOT speed
        ↓
Next question → Repeat
        ↓
Final Leaderboard
```

---

## Accuracy Mode Rules
- Individual player mode.
- Scoring is based **only on correctness**, NOT on speed.
- Correct answer: +100 points (flat, regardless of when answered).
- Wrong answer: 0 points.
- Streak bonus: 3 consecutive correct answers → +50 bonus points.
- Optionally, teacher may enable a time limit per question in Settings.
- Designed for assessment — encourages careful, accurate answers rather than fast guessing.

---

## Frontend Architecture

```
AccuracyModeHostView
  ├── FullscreenBackground (Egyptian stone walls + teal torch flames)
  ├── TargetIcon (top center, glowing teal)
  ├── TitleBanner ("ACCURACY MODE")
  ├── LeftPanel (QR + PIN)
  ├── PlayersPanel (real-time grid)
  ├── AnswerProgressBar (during question: X of Y answered)
  ├── StartGameButton
  └── ExitButton (top-right)
```

### Real-Time State (WebSocket)
```js
{
  gamePin: string,
  players: Player[],
  gameStatus: "lobby" | "question" | "all_answered" | "reveal" | "leaderboard",
  currentQuestion: Question | null,
  answeredCount: number,
  totalPlayers: number
}
```

---

## Backend Architecture

### Game Session API
```
POST /api/games/accuracy/create
Body: { quizId: string, timeLimitPerQuestion?: number }
Response: { gamePin: string, gameId: string }

POST /api/games/accuracy/:gameId/start
Response: { status: "started", firstQuestion: Question }

WS /ws/games/:gameId
→ Events: player_joined, answer_submitted
← Events: question_start, all_answered, answer_reveal, game_over
```

### Scoring Logic
```
correct answer → +100 points
streak (3 in a row) → +50 bonus
wrong answer → 0 points
```

---

## Sound & Audio
- **Lobby Music**: uploadable ambient track for the waiting phase.
- **Player Join Sound**: short chime.
- **All Players Answered Sound**: short notification ding ("Everyone has answered").
- **Correct Answer Reveal**: bright chime.
- **Wrong Answer Reveal**: low buzz.
- **Streak Sound**: special ascending chime on 3-streak milestone.
- **Final Leaderboard Music**: victory/celebration music.

### Assigning Sounds (Settings → Sound → Accuracy Mode)
Upload custom `.mp3`, `.wav`, or `.ogg` for each slot. Factory defaults used if not customized.

---

## Accuracy Mode vs Classic Mode Comparison

| Feature | Classic Mode | Accuracy Mode |
|---------|-------------|---------------|
| Score basis | Speed + Correctness | Correctness only |
| Timer | Required | Optional |
| Encourages | Fast thinking | Careful thinking |
| Streak bonus | No | Yes |
| Best for | Review games | Assessments |
