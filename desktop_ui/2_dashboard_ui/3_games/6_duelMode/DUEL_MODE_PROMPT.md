# EduVerse — Duel Mode (Desktop / Teacher Host View) UI Prompt

---

## Overview

**Duel Mode** is a head-to-head 1-vs-1 competitive quiz format where two individual students (or two teams) face each other in a direct challenge. The teacher selects the quiz, sets up the duel, and monitors the battle on the big screen.

Duel Mode is accessible from the **Available Quizzes** card menu → "Duel Mode."

---

## Visual Design Prompt

### Overall Aesthetic
- Dark, high-energy arena theme — similar deep navy background as Classic Mode but with dramatic split-screen layout dividing left challenger vs right challenger.
- Neon accent colors split: left side = blue/cyan, right side = red/orange.
- Arena-style atmosphere: spotlights pointing down from top, crowd silhouette at bottom (optional), electric energy lines in center divider.
- "VS" divider in center with lightning bolt / electrical crackle animation.

### Screen 1 — Duel Lobby / Setup

**Background**: Full-screen dark arena with spotlight effects and neon tech grid.

**Title Banner** (top-center): "**DUEL MODE**" in bold white text with neon red-and-blue split glow.

**Left Side (Player 1 / Team A)**:
- Dark card with cyan/blue border.
- "PLAYER 1" label.
- Avatar placeholder (glowing blue silhouette).
- Player name appears when they join.
- "SCAN TO JOIN" QR code and/or Game PIN.

**Center**:
- Large "**VS**" text with electric crackle animation.
- A countdown or "WAITING..." pulsing indicator.

**Right Side (Player 2 / Team B)**:
- Dark card with red/orange border.
- "PLAYER 2" label.
- Avatar placeholder (glowing red silhouette).
- Player name appears when they join.

**Bottom**: "WAITING FOR BOTH PLAYERS TO JOIN..." status text.

**Start Button**: Appears once both players have joined.

---

### Screen 2 — Active Duel (Question Phase)

**Left side**: Player 1 avatar + name + current score. Answer choice buttons on left.
**Right side**: Player 2 avatar + name + current score. Answer choice buttons on right.
**Center**: Question text displayed in a shared panel. Timer/countdown shown center-top.

Both players answer simultaneously on their own devices. Teacher screen shows who answered first (indicator appears).

---

### Screen 3 — Round Result

After each question:
- Winner of that round gets a point/star added to their side.
- Animated "POINT!" burst on the winning side.
- Brief 2-second transition before next question.

---

### Screen 4 — Duel Winner / Final

**Full screen**: Split reveal — winner's side glows brightly, loser's side dims.
- Large "WINNER!" banner on winning side with confetti.
- Final score displayed (e.g., 7 – 3).
- Optional: "REMATCH?" button for teacher.

---

## Duel Mode Rules

- **1-vs-1 format**: Two individual players OR two teams.
- Questions from the selected quiz are asked one at a time.
- Both players/teams answer simultaneously.
- **Speed + Accuracy**: First correct answer wins the round point.
- If both correct at same time → point split or fastest wins (configurable).
- If both wrong → no points awarded.
- First to reach a set number of round wins (e.g., 5) = WINNER. OR: most points after all questions.
- Teacher configures: total questions, win threshold, time limit per question.

---

## Frontend Architecture

```
DuelModeHostView
  ├── ArenaBackground (spotlights + electric grid)
  ├── TitleBanner ("DUEL MODE")
  ├── LobbyScreen
  │     ├── PlayerSlot (left, blue, Player 1)
  │     ├── VSCenterDivider (animated lightning)
  │     └── PlayerSlot (right, red, Player 2)
  ├── ActiveDuelScreen
  │     ├── PlayerHUD (left: avatar + score + answer buttons)
  │     ├── QuestionPanel (center: question text + timer)
  │     └── PlayerHUD (right: avatar + score + answer buttons)
  ├── RoundResultOverlay (point awarded animation)
  └── WinnerScreen
        ├── WinnerSide (full glow + confetti)
        ├── FinalScoreDisplay
        └── RematchButton
```

---

## Backend Architecture

### API Endpoints
```
POST /api/games/duel/create
Body: { quizId: string, winThreshold: number, timeLimitPerQuestion: number }
Response: { gamePin, gameId, player1Slot: string, player2Slot: string }

POST /api/games/duel/:gameId/start
WS   /ws/games/:gameId
→ Events: player1_answered, player2_answered
← Events: question_start, round_result, duel_winner
```

### Scoring Logic
```
if (player1_correct && player1_time < player2_time) → player1 wins round
if (player2_correct && player2_time < player1_time) → player2 wins round
if (both_correct && same_time) → both +0.5 point
if (both_wrong) → no points
```

---

## Sound & Audio (Duel Mode)

| Sound Slot | Trigger | Default |
|------------|---------|---------|
| Lobby Music | Waiting for both players | Tense battle ambient |
| Player 1 Joins | Player 1 connects | Blue electric sound |
| Player 2 Joins | Player 2 connects | Red electric sound |
| Duel Start | Both joined, teacher starts | Battle horn clash |
| Question Reveal | Question appears | Quick whoosh |
| Player Answers | Either player taps answer | Click/buzz sound |
| Round Point — P1 | Player 1 wins round | Cyan victory chime |
| Round Point — P2 | Player 2 wins round | Red victory chime |
| Both Wrong | No points this round | Low drone |
| Final Winner | Game ends | Championship fanfare |

### Assigning Sounds (Settings → Sound → Duel Mode)
Upload `.mp3`, `.wav`, or `.ogg` for each slot.
