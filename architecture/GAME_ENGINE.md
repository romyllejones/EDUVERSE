# EduVerse — Game Engine Architecture

---

## Overview

All game modes share a common **Game Session Controller** with mode-specific plugins that handle scoring, state transitions, and special rules.

---

## Game Session State Machine

Every game mode follows the same top-level state machine:

```
lobby
  ↓  (teacher starts)
question_announce      ← shown on teacher screen, students see "get ready"
  ↓  (auto-advance after X seconds)
question_active        ← students can submit answers; timer running
  ↓  (all answered OR timer ends)
answer_reveal          ← correct answer shown on both teacher + student screens
  ↓  (auto-advance or teacher clicks next)
[repeat question_announce → question_active → answer_reveal for each question]
  ↓  (last question done)
leaderboard            ← final standings shown
  ↓
finished               ← session closed, data saved
```

---

## Base Game Controller (Shared)

```
GameSessionController
  ├── createSession(quizId, gameMode, config) → { gamePin, gameId }
  ├── joinSession(gamePin, studentId) → { playerId, playerInfo }
  ├── startGame(gameId) → emits first question
  ├── submitAnswer(gameId, playerId, answer, timeTakenMs, extras?)
  ├── advanceToNextQuestion(gameId)
  ├── endGame(gameId) → saves final scores + rankings
  └── getLeaderboard(gameId) → sorted player/team rankings
```

---

## Mode-Specific Plugins

### Classic Mode Plugin
```
ClassicModePlugin
  scoring:
    correct → Math.max(100, Math.round(1000 × (timeLimit - timeTaken) / timeLimit))
    wrong   → 0
  specialRules:
    none
  endCondition:
    all questions exhausted
```

### Accuracy Mode Plugin
```
AccuracyModePlugin
  scoring:
    correct            → +100 pts (flat)
    correct (3-streak) → +150 pts (+50 streak bonus)
    wrong              → 0 pts (streak resets)
  state:
    tracks consecutiveCorrect per player
  endCondition:
    all questions exhausted, OR all players answered (if no timer)
```

### Confidence Mode Plugin
```
ConfidenceModePlugin
  input:    answer + confidenceLevel (1|2|3)
  scoring:
    correct → +100 × confidenceLevel
    wrong   → -50  × confidenceLevel
  constraint:
    player.totalScore = Math.max(0, player.totalScore + delta)  // no negative total
  endCondition:
    all questions exhausted
```

### Lost Pyramid Plugin
```
LostPyramidPlugin
  setup:
    2–4 teams, each starts at step 1
    pyramid has N steps (default 15, configurable)
  scoring per question:
    majority of team correct → team advances +stepReward (default +2) steps
    majority wrong           → team stays OR -1 step (if penalty enabled)
  winCondition:
    first team to reach step N → immediate game over + winner declared
  fallback endCondition:
    all questions exhausted → team at highest step wins
  tiebreaker:
    higher total individual score within tied teams
```

### Color Kingdom Plugin
```
ColorKingdomPlugin
  setup:
    2–4 teams, hex grid (small=60/medium=90/large=120 hexes)
    each team starts with 3 hexes in their corner
  scoring per question:
    hexes_gained = floor(correctCount / teamSize × maxHexesPerRound)
    maxHexesPerRound = 5 (configurable)
    hexes conquered from: adjacent neutral hexes first, then contested border hexes
  contested hex resolution:
    if two teams both get correct majority → faster team wins contested hexes
  winCondition:
    team controls ≥60% of total hexes → immediate win
  fallback endCondition:
    all questions exhausted → team with most hexes wins
```

### Duel Mode Plugin
```
DuelModePlugin
  setup:
    exactly 2 players or 2 teams
  scoring per round:
    first correct answer → round win (1 point)
    both correct same time → 0.5 point each
    both wrong → 0 points
  winCondition:
    first to reach winThreshold (default 5 round wins)
  fallback:
    all questions exhausted → higher round wins total wins
    if tied → compare total response speed
```

---

## WebSocket Event Reference (Complete)

### Teacher → Server
| Event | Payload | Description |
|-------|---------|-------------|
| `game:start` | `{ gameId }` | Start game from lobby |
| `game:next` | `{ gameId }` | Advance to next question |
| `game:end` | `{ gameId }` | Force end game |
| `game:kick` | `{ gameId, playerId }` | Remove a player |

### Server → Teacher
| Event | Payload | Description |
|-------|---------|-------------|
| `lobby:player_joined` | `{ player }` | New player in lobby |
| `lobby:player_left` | `{ playerId }` | Player disconnected |
| `question:announce` | `{ questionNumber, total }` | Question number intro |
| `question:active` | `{ question, timeLimit }` | Full question + choices |
| `answer:progress` | `{ answered, total }` | Live count of submissions |
| `answer:reveal` | `{ correctAnswer, stats }` | Reveal correct answer |
| `score:update` | `{ leaderboard }` | Updated rankings |
| `game:over` | `{ finalLeaderboard }` | Game ended |

### Server → Student
| Event | Payload | Description |
|-------|---------|-------------|
| `game:started` | `{ gameMode }` | Game is now starting |
| `question:active` | `{ questionText, choices, questionNumber, total, timeLimit }` | Question for student |
| `answer:result` | `{ isCorrect, correctAnswer, pointsEarned, newScore, [extras] }` | Result of their answer |
| `question:next` | `{ questionNumber }` | Next question coming |
| `game:over` | `{ rank, finalScore, accuracy }` | Final result for this student |

### Student → Server
| Event | Payload | Description |
|-------|---------|-------------|
| `answer:submit` | `{ questionId, answer, timeTakenMs, confidenceLevel? }` | Submit answer |
| `team:join` | `{ teamId }` | Join a team (Pyramid / Color Kingdom) |

---

## Scoring Summary Table

| Mode | Correct Points | Wrong Points | Speed Factor | Team/Individual |
|------|---------------|-------------|-------------|----------------|
| Classic | 100–1000 (speed-scaled) | 0 | Yes | Individual |
| Accuracy | +100 flat | 0 | No | Individual |
| Confidence L1 | +100 | 0 | No | Individual |
| Confidence L2 | +200 | -100 | No | Individual |
| Confidence L3 | +300 | -150 | No | Individual |
| Lost Pyramid | +2 steps (team) | 0 or -1 step | First correct | Team |
| Color Kingdom | +N hexes (team) | 0 hexes | First correct (contested) | Team |
| Duel | +1 round point | 0 | First correct | Individual / Team |
