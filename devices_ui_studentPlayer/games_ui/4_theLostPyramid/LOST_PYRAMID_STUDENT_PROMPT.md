# EduVerse — The Lost Pyramid (Student Device / Player View) UI Prompt

## Reference Images (in order)
1. `1_welcome_player.png`
2. `2_choose_team.png`
3. `3_team_selected.png`
4. `4_reveal_question.png`
5. `5_wating_for_correct_anwer.png`
6. `6_answer_if_correct.png`
7. `6_answer_if_wrong.png`
8. `7_waiting_for_next_question.png`

---

## Visual Design Prompt

---

### Screen 1 — Welcome Player (Joined Lobby)

Same layout as Classic Mode welcome screen:
- EduVerse avatar logo at top.
- "WELCOME PLAYER!" heading.
- "You're in! Let's get ready to play." subtitle.
- Game ID badge.
- Player info card (ID Number, English Name, Mathayum No, Section and Class).
- Animated hourglass ring + "WAITING FOR THE GAME TO START..." pulsing text.

---

### Screen 2 — Select Your Team ⭐ (UNIQUE TO PYRAMID)

**Background**: Deep dark navy with cyan glow at bottom.

**Header**:
- EduVerse glowing avatar logo at top-center.
- "**EduVerse**" brand text below.
- "**SELECT YOUR TEAM**" in bold white caps with cyan dash decorators.
- Subtitle: "Join a team and start your journey together." in small muted text.

**Team Selection List** (4 rows, stacked vertically):

Each row is a full-width card with:
- **Left**: Egyptian character avatar/icon (circular, ornate golden frame).
  - Team 1 = Anubis (black jackal deity).
  - Team 2 = The Pharaohs (golden Tutankhamun mask).
  - Team 3 = The Mummies (mummy character, bandaged).
  - Team 4 = The Horus (golden falcon bird).
- **Center**: Team name (bold white, e.g., "**The Anubis**") + member count in small cyan text ("48 members").
- **Right**: "**JOIN THIS TEAM**" button (dark blue bordered button, rounded, cyan text).

**Bottom**: Back arrow button (circular, dark outline with left arrow) to return if needed.

---

### Screen 3 — Team Confirmed / Selected

After tapping "JOIN THIS TEAM":
- Brief confirmation animation.
- Screen shows: selected team's avatar + "**YOU'VE JOINED [Team Name]!**" message.
- Returns to waiting state: hourglass animation + "Waiting for all teams to be ready..."

---

### Screen 4 — Question Reveal

Same layout as Classic Mode question screen:
- Question counter at top.
- Question text card.
- ABCD answer choices (color coded A=red, B=blue, C=green, D=purple with geometric icons).

**Key behavior**: This student's answer contributes to the **team's collective answer**. The team's majority correct answer determines pyramid advancement.

---

### Screen 5 — Waiting for Team's Answer (Waiting for Reveal)

Selected answer locked and highlighted. Waiting text shown: "Waiting for your team's result..."

---

### Screen 6a — Correct (Team Advances)

Same green checkmark overlay + "CORRECT!" text.

Optional team result message: "Your team advances **+2 steps**!" in gold text.

---

### Screen 6b — Wrong (Team Held or Falls)

Same red X overlay + "INCORRECT" text.

Optional team message: "Your team is held back." or "Your team falls back 1 step." in red text.

---

### Screen 7 — Waiting for Next Question

Brief waiting state before next question appears.

---

## Frontend Architecture (Mobile)

```
LostPyramidStudentView
  ├── WelcomeScreen (same as Classic)
  ├── TeamSelectScreen ⭐
  │     ├── EduVerseHeader (logo + "SELECT YOUR TEAM")
  │     └── TeamList
  │           └── TeamRow[]
  │                 ├── TeamAvatarIcon (character + golden frame)
  │                 ├── TeamInfo (name + member count)
  │                 └── JoinTeamButton
  ├── TeamConfirmedScreen
  │     ├── SelectedTeamAvatar
  │     └── ConfirmationMessage + WaitingLoader
  ├── QuestionScreen (same as Classic)
  ├── AnswerLockedScreen
  ├── TeamResultOverlay
  │     ├── CorrectTeamOverlay (checkmark + "CORRECT!" + "+2 steps" message)
  │     └── WrongTeamOverlay (X + "INCORRECT" + team status)
  └── WaitingNextScreen
```

---

## WebSocket Events (Student Device — Lost Pyramid)
```
← Receives:
  team_options          → { teams: [{ id, name, character, memberCount }] }
  team_joined_confirmed → { teamId, teamName }
  question_revealed     → { question, choices, questionNumber }
  team_answer_result    → { teamIsCorrect, correctAnswer, stepsGained, teamCurrentStep }
  game_over             → { winnerTeam, finalPositions }

→ Sends:
  join_team             → { teamId }
  submit_answer         → { questionId, selectedChoice, timeTakenMs }
```
