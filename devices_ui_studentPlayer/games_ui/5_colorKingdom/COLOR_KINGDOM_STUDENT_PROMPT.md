# EduVerse — Color Kingdom (Student Device / Player View) UI Prompt

## Reference Image
`1_welcome_player.png`

---

## Visual Design Prompt

Same deep dark navy space theme as all other student screens. Portrait orientation.

---

### Screen 1 — Welcome Player (Joined Lobby)

Identical to the welcome screen shared by all game modes:
- EduVerse glowing avatar logo at top.
- "WELCOME PLAYER!" in bold white.
- "You're in! Let's get ready to play." subtitle.
- Game ID badge (controller icon + code).
- Player info card:
  - ID Number
  - English Name
  - Mathayum No
  - Section and Class Number
- Animated hourglass ring + "WAITING FOR THE GAME TO START..." pulsing text.

> Note: All fields come from the student database. Thai Name is stored but English Name is displayed on the welcome screen by default. Teachers can configure which name is displayed in Settings.

---

### Screen 2 — Choose Your Team (Student Side)

After the teacher advances from the lobby, students see a team selection screen:

**Header**: EduVerse logo + "SELECT YOUR TEAM" title.
**Team cards**: same 4 options (Queen of Hearts / Alice / Medusa / Atlantis) as the teacher view but formatted for portrait mobile.

Each team row:
- Character icon (circular, ornate frame).
- Team name + current member count.
- "JOIN THIS TEAM" button.

After joining a team:
- Confirmation screen: "You've joined [Team Name]!" + team character illustration.
- Returns to waiting state until teacher starts the game.

---

### Screen 3 — Active Question

Same as Classic Mode question screen:
- Question counter at top.
- Question text card.
- ABCD answer choices (color coded).

Student's answer contributes to the **team's collective performance**. More teammates answering correctly = more hexes gained this round.

---

### Screen 4 — Answer Result

- CORRECT: green checkmark + "CORRECT!" + optional "Your team gained [N] hexes!"
- WRONG: red X + "INCORRECT" + correct answer shown.

---

### Screen 5 — Waiting for Next Question

Brief waiting state. Optionally shows a mini hex-territory preview (team's current hex count vs others).

---

## WebSocket Events (Student — Color Kingdom)
```
← Receives:
  team_options          → { teams: [{ id, name, character, color, memberCount }] }
  team_joined_confirmed → { teamId, teamName, teamColor }
  question_revealed     → { question, choices, questionNumber }
  team_answer_result    → { teamCorrect, correctAnswer, hexesGained, teamTotalHexes }
  game_over             → { winnerTeam, myTeamFinalHexes, finalHexMap }

→ Sends:
  join_team             → { teamId }
  submit_answer         → { questionId, selectedChoice, timeTakenMs }
```
