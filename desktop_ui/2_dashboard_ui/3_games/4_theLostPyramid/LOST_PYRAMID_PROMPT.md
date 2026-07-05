# EduVerse — The Lost Pyramid (Desktop / Teacher Host View) UI Prompt

## Reference Images (in order)
1. `1_lobby_waiting_for_players.png`
2. `2_team_are_ready.png`
3. `3_a_previewTEAM1.png` — `3_d_previewTEAM4.png`
4. `4_welcome_to_the_lost_pyramid.png`
5. `5_preview_of_initial_player_placement.png`
6. `6_Question1.png`
7. `7_revealQuestion1.png`
8. `8_time_sUp.png`
9. `9_answerReveal.png`
10. `10_correct.png`
11. `10_incorrect.png`
12. `11_teamPlacement.png`
13. `12_oneGroupReachesTheTop.png`
14. `13_winner_proclaimation.png`
15. `pyramidBlocksView.png`

---

## Visual Design Prompt

**The Lost Pyramid** is a team-based adventure game with a rich ancient Egypt aesthetic. Every screen must feel cinematic and immersive.

---

### Screen 1 — Lobby: Waiting for Players
**Background**: Full-screen photorealistic ancient Egyptian interior — a torch-lit stone corridor/chamber leading to a grand pyramid in the distance. Warm amber and gold tones. Stone walls, floor tiles, glowing fire torches on both sides.

**Text**: "**The Lost Pyramid**" title (elegant serif/ancient-script style) in gold at the top, with small decorative "· The ·" above it.

**Center**: Large bold glowing gold text — "**WAITING FOR PLAYERS**" centered on the pyramid image.

**Top-left corner**: QR Code panel in gold-bordered dark card. Below QR: "SCAN TO JOIN". Below: "GAME ID: 482731".

**Top-right corner**: Small exit/back icon button.

---

### Screen 2 — Team Are Ready
Same Egyptian background. Shows a "TEAMS ARE READY" or "TEAMS FORMED" status banner in gold. Teacher can see team assignments before revealing them one by one.

---

### Screens 3a–3d — Team Preview Cards (shown sequentially)
Each team gets a cinematic reveal screen:
- Egyptian pyramid background with dramatic golden sunset sky and hieroglyphic columns.
- Egyptian deity/character illustration beside the team name (Team 1 = Anubis, Team 2 = Pharaoh/Tutankhamun, Team 3 = Mummy, Team 4 = Horus/Falcon).
- Large "**TEAM [N]**" text in gold inside a dark ornate banner.
- Below: a dark gold-bordered panel listing team member names (2 per row, with small person icons).
- QR code + "SCAN TO JOIN" still visible in top-left.

---

### Screen 4 — Welcome to The Lost Pyramid (Game Start)
Full cinematic photorealistic Egyptian pyramid exterior — dramatic golden sunset sky, ancient columns with hieroglyphs. Torches in foreground.

Center text: "**WELCOME TO**" in smaller gold text, below: "**THE LOST PYRAMID**" in very large, bold, glowing gold text with sparkle/light-ray effects.

This screen is shown for 3–5 seconds as a cinematic intro before transitioning to the pyramid game board.

---

### Screen 5 — Initial Pyramid Placement / Pyramid Blocks View
Full-screen cinematic pyramid view from outside — blazing golden sky, Egyptian columns.

**Pyramid Structure**: A stepped pyramid rendered in photorealistic 3D-style with 15 tiers (steps numbered 1–15 from bottom to top). Each tier has an ornate golden scarab-winged numbered badge.

**At the apex**: The Anubis god figure (dark imposing deity with staff and crown) sits atop the pyramid.

**Team Positions**: Each team's avatar icon (circular portrait of their Egyptian deity character) appears on their current step number. All teams start at step 1 (bottom).

**Goal**: First team to reach step 15 (top) wins.

**Initial state**: All 4 team avatars clustered at step 1.

---

### Screen 6 — Question Announce Screen
Cinematic — photorealistic Egyptian exterior (pyramid, golden sky, columns).

Left side: Egyptian beauty character (Cleopatra-style) standing in full body view.
Right center: A dark ornate golden-bordered panel showing "**QUESTION [N]**" in large gold text with scarab decoration above and below.

This is the question number announcement screen before the actual question text appears.

---

### Screen 7 / 8 — Active Question (Answer Phase)
**Layout**:
- **Top bar**: 4 team scorecards (Team 1, Team 2, Team 3, Team 4), each with team avatar, colored step-progress indicator bars (5 small filled/empty squares).
- **Top-right**: "QUESTION [N]" badge in dark gold ornate frame.
- **Left side**: Cleopatra character standing.
- **Center**: Dark ornate question card with golden borders and scarab emblem:
  - Question text in white serif font.
- **Bottom**: 4 answer choice buttons arranged in 2×2 grid:
  - A (red/maroon background, red triangle icon, Team 1 Anubis avatar on edge)
  - B (blue background, Team 2 Pharaoh avatar on edge)
  - C (green background, Team 3 Mummy avatar on edge)
  - D (purple background, Team 4 Horus avatar on edge)
- **Timer**: Countdown timer visible (clock animation, sand/countdown effect).

On Time's Up: Choices become locked; "TIME'S UP" animation plays.

---

### Screen 9 — Answer Reveal
Same layout as active question. The correct answer button glows brightly; incorrect buttons dim. Team avatars appear beside the answer they chose.

---

### Screen 10a — "You May Pass" (Correct Answer Result)
Dramatic full-screen pyramid view (from outside, green/golden daylight sky).

Center: Anubis god figure standing beside the pyramid, pointing at the correct team's new position.

Large golden banner at top: "**YOU MAY PASS**" with wings/hieroglyph decorations.

Animated: winning team's avatar moves UP the pyramid steps (e.g., +2 STEPS badge appears in a golden coin graphic).

---

### Screen 10b — Incorrect Result
Darker, more ominous version of the pyramid view.

Banner: "**HELD BACK**" or similar failure message in red/dark gold tones.

Team that answered wrong does not advance (or moves back 1 step if penalty enabled).

---

### Screen 11 — Team Placement (Pyramid Progress View)
Portrait-oriented full-screen pyramid view (tall format).

The stepped pyramid fills the screen. Each step 1–15 labeled. Team avatar icons shown at their current positions on the steps. Anubis watches from the apex.

Dramatic golden sky, Egyptian columns flanking both sides, fire torches at bottom corners.

---

### Screen 12 — One Group Reaches the Top
Dramatic: winning team's avatar is at step 15 next to Anubis. Celebration particles/gold confetti rain.

---

### Screen 13 — Winner Proclamation
Grand cinematic finale:
- Full pyramid view, blazing golden sky, crowds of Egyptian people in foreground with hands raised.
- Anubis standing to the right, pointing at winner.
- Large gold wings "**WINNER**" banner at top center.
- Winning team avatar + step 15 badge prominently displayed.
- Other teams shown at their final positions.
- Gold confetti raining down.

---

## Game Rules — The Lost Pyramid

- **Team-based** (2–4 teams, configurable).
- Each team has 2–10 members who join together.
- Teams select Egyptian-themed characters during lobby (Anubis, Pharaoh, Mummy, Horus).
- Pyramid has **15 steps** (configurable: 10–20).
- For each question: team members answer individually on their devices.
- Team score per question = majority correct answers from team members.
- **Correct**: team advances +2 steps (configurable).
- **Wrong**: team stays OR moves back -1 step (optional penalty, configured by teacher).
- First team to reach step 15 = WINNER.
- If time runs out (max questions exceeded): team at highest step wins.

---

## Frontend Architecture

```
LostPyramidHostView
  ├── LobbyScreen
  │     ├── EgyptianBackground
  │     ├── QRCodePanel (top-left)
  │     ├── GameTitle ("The Lost Pyramid")
  │     └── WaitingText (animated pulse)
  ├── TeamRevealScreens (3a–3d, auto-cycling)
  │     ├── CinematicBackground
  │     ├── TeamCharacterIllustration
  │     ├── TeamNameBanner
  │     └── TeamMembersList
  ├── WelcomeIntroScreen (4) (auto-advance after 4s)
  ├── PyramidBoardScreen (5, 11)
  │     ├── PyramidBackground
  │     ├── StepTiles[] (1–15, numbered, golden badges)
  │     ├── TeamAvatars[] (positioned by current step)
  │     └── AnubisApex
  ├── QuestionAnnounceScreen (6)
  │     ├── CleopatraCharacter
  │     └── QuestionNumberBanner
  ├── ActiveQuestionScreen (7, 8)
  │     ├── TeamScoreBar[] (top)
  │     ├── CleopatraCharacter
  │     ├── QuestionCard
  │     ├── AnswerButtons[] (A, B, C, D)
  │     └── CountdownTimer
  ├── CorrectResultScreen (10a)
  └── WinnerProclamationScreen (13)
```

---

## Backend Architecture

### API Endpoints
```
POST /api/games/pyramid/create
Body: { quizId: string, numberOfTeams: 2|3|4, stepsToWin: number }
Response: { gamePin, gameId, teams: Team[] }

POST /api/games/pyramid/:gameId/start
WS   /ws/games/:gameId
```

### Database Tables
```sql
pyramid_teams (
  id          UUID PRIMARY KEY,
  game_id     UUID REFERENCES game_sessions(id),
  team_number INTEGER,
  team_name   TEXT,
  character   TEXT,      -- 'anubis' | 'pharaoh' | 'mummy' | 'horus'
  current_step INTEGER DEFAULT 1,
  color       TEXT
)
```

---

## Sound & Audio (The Lost Pyramid)

| Sound Slot | Trigger | Default |
|------------|---------|---------|
| Lobby Music | Waiting for players | Epic Egyptian ambient |
| Player Join | New student joins | Torch flicker sound |
| Game Start Fanfare | Welcome to the Lost Pyramid screen | Cinematic orchestral swell |
| Question Announce | Question number reveal | Mysterious horn |
| Timer Tick | Last 5 seconds | Sand timer ticking |
| Time's Up | Timer hits 0 | Dramatic gong |
| Correct Answer | Team advances | Triumphant Egyptian horn |
| Wrong Answer | Team held | Low ominous drone |
| Team Advances | Avatar moves up pyramid | Step-climb sound effect |
| Team Falls Back | Avatar moves down | Slide-back sound |
| One Team Reaches Top | First to step 15 | Grand fanfare burst |
| Winner Proclamation | Final screen | Epic victory anthem |

### Assigning Sounds (Settings → Sound → The Lost Pyramid)
Upload `.mp3`, `.wav`, or `.ogg` for each slot.
