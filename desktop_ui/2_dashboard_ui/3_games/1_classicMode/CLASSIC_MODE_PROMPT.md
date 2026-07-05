# EduVerse — Classic Mode (Desktop / Teacher Host View) UI Prompt

## Reference Image
`1_waiting_for_players.png`

---

## Visual Design Prompt

Build the **Classic Mode** game host screen for the teacher's desktop display. This is a full-screen game view that replaces the dashboard UI while a game is active.

### Screen: Lobby / Waiting for Players

**Background**
- Full-screen deep navy/dark blue with a faint glowing hex-grid or tech-circuit pattern.
- The entire floor area has a neon-blue glowing circular holographic ring/platform effect radiating outward from the center-bottom, like a stage spotlight.
- Ambient neon-blue glow lines radiate outward from the center title, giving a sci-fi arena feel.

**Header / Title Banner**
- Centered at the top, a tall rectangular banner with clipped corners and a neon blue border.
- Above the banner: a glowing 3D trophy icon (blue outline, star on cup), centered.
- Inside banner: title text "**CLASSIC MODE**" in large white, bold, uppercase with slight emboss/glow.
- Below title (inside banner): subtitle text "— WAITING FOR PLAYERS TO JOIN —" in smaller cyan uppercase.

**Left Panel — Join Info**
- Rectangular card with a dark navy background, bright neon-blue border.
- Top label: "**SCAN TO JOIN**" in white caps.
- Large QR Code displayed clearly, white background with black QR pattern.
- Separator with "OR" label centered between two horizontal lines.
- Below: "**GAME PIN**" label.
- Large, prominent Game PIN number (e.g., `482915`) in bright cyan bold text, displayed in a dark inset pill/box.

**Center Panel — Players Joined**
- Large dark rectangle (the main content area), neon-blue border, slightly rounded corners.
- Initially empty (no players have joined yet).
- As players join, their avatar/name tiles appear in this area in a grid, animating in from the center.
- Each player tile: dark card with player avatar icon, player name below.

**No Top Navigation / Sidebar**
- This is a full-screen game view. No sidebar or header from the dashboard is shown.

---

## Game Flow (Classic Mode)

```
Teacher selects Classic Mode from Quiz card (Available Quizzes)
        ↓
System generates a Game PIN (6-digit random)
System generates QR Code linking to: [app-url]/join?pin=XXXXXX
        ↓
Lobby screen displays (this screen)
Students scan QR or enter PIN on their device
        ↓
Students appear in the center players panel (real-time)
        ↓
Teacher clicks "START GAME" button (appears when ≥1 player joins)
        ↓
Question 1 revealed on teacher screen + sent to all student devices simultaneously
        ↓
Students answer on their devices — teacher screen shows live answer count
        ↓
Timer expires or all answer → Answer reveal + score update
        ↓
Repeat for each question
        ↓
Final Leaderboard screen → Game Over
```

---

## Classic Mode Rules
- Individual player mode (no teams).
- Each correct answer scores points based on **speed** — faster correct answers = more points.
- Maximum points per question: 1000; minimum (last to answer correctly): ~100.
- Wrong answers: 0 points.
- No penalty for skipping.
- Leaderboard updates live after each question.

---

## Frontend Architecture

```
ClassicModeHostView
  ├── FullscreenBackground (animated grid + glow)
  ├── TrophyIcon (top center, glowing)
  ├── TitleBanner ("CLASSIC MODE" + "WAITING FOR PLAYERS")
  ├── LeftPanel
  │     ├── ScanToJoinLabel
  │     ├── QRCodeDisplay
  │     ├── OrDivider
  │     └── GamePinDisplay
  ├── PlayersPanel
  │     ├── EmptyState (pulsing "Waiting..." text)
  │     └── PlayerGrid
  │           └── PlayerTile[] (avatar + name, animate-in)
  ├── StartGameButton (appears after ≥1 player joins)
  └── ExitButton (top-right corner, returns to dashboard)
```

### Real-Time State (WebSocket)
```js
{
  gamePin: string,          // 6-digit code
  qrCodeUrl: string,        // encoded join URL
  players: [
    { id: string, name: string, avatar: string, joinedAt: timestamp }
  ],
  gameStatus: "lobby" | "question" | "reveal" | "leaderboard" | "finished"
}
```

---

## Backend Architecture

### Game Session API
```
POST /api/games/classic/create
Body: { quizId: string }
Response: { gamePin: string, gameId: string, sessionToken: string }

GET /api/games/classic/:gameId/players
Response: { players: Player[] }

POST /api/games/classic/:gameId/start
Response: { status: "started", firstQuestion: Question }

WS  /ws/games/:gameId
→ Events received: player_joined, player_left, answer_submitted
← Events emitted: question_start, question_end, answer_reveal, game_over
```

### Database Tables
```sql
game_sessions (
  id            UUID PRIMARY KEY,
  quiz_id       UUID REFERENCES quizzes(id),
  game_pin      CHAR(6) UNIQUE,
  mode          TEXT DEFAULT 'classic',
  status        TEXT DEFAULT 'lobby',
  created_by    UUID REFERENCES users(id),
  started_at    TIMESTAMP,
  ended_at      TIMESTAMP,
  created_at    TIMESTAMP DEFAULT NOW()
)

game_players (
  id          UUID PRIMARY KEY,
  game_id     UUID REFERENCES game_sessions(id),
  student_id  UUID REFERENCES students(id),
  score       INTEGER DEFAULT 0,
  rank        INTEGER,
  joined_at   TIMESTAMP DEFAULT NOW()
)

game_answers (
  id              UUID PRIMARY KEY,
  game_id         UUID REFERENCES game_sessions(id),
  player_id       UUID REFERENCES game_players(id),
  question_id     UUID REFERENCES questions(id),
  selected_answer TEXT,
  is_correct      BOOLEAN,
  time_taken_ms   INTEGER,
  points_earned   INTEGER DEFAULT 0,
  answered_at     TIMESTAMP DEFAULT NOW()
)
```

---

## Sound & Audio
- **Lobby music**: looping ambient background music while waiting (uploadable via Settings → Sound → Classic Mode → Lobby Music).
- **Player join sound**: short chime/ding each time a new player tile appears.
- **Game start sound**: epic fanfare/countdown sound when teacher clicks Start.
- **Question reveal sound**: dramatic "whoosh" when question appears.
- **Timer tick sound**: ticking sound in the last 5 seconds of a question.
- **Correct answer reveal**: triumphant chord.
- **Wrong answer reveal**: low buzz.
- **Leaderboard reveal music**: celebratory music.
- **Winner sound**: victory fanfare.

### Assigning Sounds (in Settings → Sound → Classic Mode)
Each sound slot accepts: `.mp3`, `.wav`, `.ogg` file uploads. Default factory sounds are used if no custom file is uploaded.

---

## Scoring Algorithm
```
base_score = 1000
time_bonus_factor = (time_limit_ms - time_taken_ms) / time_limit_ms
points = Math.round(base_score * time_bonus_factor)
minimum_points_for_correct = 100
final_points = Math.max(points, minimum_points_for_correct)
```
