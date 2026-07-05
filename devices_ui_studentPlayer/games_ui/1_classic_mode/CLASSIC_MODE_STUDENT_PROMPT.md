# EduVerse — Classic Mode (Student Device / Player View) UI Prompt

## Reference Images (in order)
1. `1_welcome_player.png`
2. `2_reveal_question.png`
3. `3_wating_for_correct_anwer.png`
4. `4_answer_if_correct.png`
5. `4_answer_if_wrong.png`
6. `5_waiting_for_next_question.png`

---

## Visual Design Prompt

All student screens share a **deep dark navy / space theme** with cyan/blue accent neon glow. Portrait orientation, optimized for mobile devices.

---

### Screen 1 — Welcome Player (Joined, Waiting for Game)

**Background**: Deep dark navy with faint stars/particles.

**Header (top)**:
- Small dark pill/badge: controller icon + "GAME ID" label + bold code (e.g., "**A7B3K9**").

**Main Card** (dark rounded card, centered):
- **ID NUMBER**: e.g., "2024-56789"
- **ENGLISH NAME**: e.g., "Juan Miguel D. Reyes"
- **MATHAYUM NO**: e.g., "16"
- **SECTION AND CLASS NUMBER**: e.g., "10 - STEM 1"
- Each field in a row with a small icon on the left and a small gray label above the value.

**Bottom Section**:
- Animated circular loading ring (hourglass icon inside, slowly rotating).
- Text: "WAITING FOR THE GAME TO START..." in cyan animated pulsing text.

---

### Screen 2 — Question Reveal (Active Question)

**Header**: Game ID badge at top.

**Question Counter**: "— QUESTION 1 OF 10 —" in small cyan caps with decorative dashes.

**Question Card**: Dark rounded rectangle, centered.
- Question text in large white centered text (wraps to multiple lines as needed).

**Answer Choices** (below question card, full-width stacked buttons):
- **A** — Red/maroon background, triangle icon on left, choice text.
- **B** — Dark blue background, circle icon on left, choice text.
- **C** — Dark green background, square icon on left, choice text.
- **D** — Dark purple background, diamond icon on left, choice text.

Each button:
- Large, rounded, full-width.
- Icon (shape) on left, text centered.
- Tap to select → button highlights with a glow/border, others dim.
- After tapping: brief haptic feedback (if supported), button locks (greyed out but selection visible).

**Bottom**: Subtle cyan horizon glow.

---

### Screen 3 — Waiting for Correct Answer (Answer Submitted)

Same layout as Screen 2 but:
- Selected answer button remains highlighted.
- Other buttons are dimmed/locked.
- A small animated spinner or text appears: "Waiting for answer reveal..." in muted text below the choices.
- No timer or submit button needed — auto-submits on selection.

---

### Screen 4a — Correct Answer

Same layout as Screen 2 but:
- A large glowing green checkmark circle animates in center-screen (overlay over the choices).
- Large "**CORRECT**" text below the checkmark in bright green.
- Optional: brief green ripple animation radiating outward.
- Correct answer button glows bright green.

---

### Screen 4b — Wrong Answer

Same layout but:
- Large glowing red X circle animates in center-screen.
- "**INCORRECT**" text in red.
- Wrong answer button glows red.
- Correct answer highlights in green (so student sees the right answer).

---

### Screen 5 — Waiting for Next Question

Background with dimmed version of answer screen.
- Text: "Waiting for the next question..." or a small timer countdown to the next question.
- Optional: mini-leaderboard snippet showing player's current rank/score.

---

## Frontend Architecture (Mobile)

```
ClassicModeStudentView
  ├── GameIDBadge (top pill)
  ├── WelcomeScreen (Screen 1)
  │     ├── PlayerInfoCard
  │     │     ├── IDNumber
  │     │     ├── EnglishName
  │     │     ├── MathayumNo
  │     │     └── SectionClass
  │     └── WaitingLoader (animated ring + pulsing text)
  ├── QuestionScreen (Screen 2)
  │     ├── QuestionCounter ("QUESTION X OF Y")
  │     ├── QuestionCard (question text)
  │     └── AnswerButtons[] (A, B, C, D — color coded)
  ├── AnswerLockedScreen (Screen 3)
  │     └── SelectedButtonHighlighted + WaitingText
  ├── ResultOverlay (Screen 4a / 4b)
  │     ├── CorrectOverlay (green checkmark + "CORRECT")
  │     └── WrongOverlay (red X + "INCORRECT" + correct answer highlight)
  └── WaitingNextScreen (Screen 5)
        └── WaitingText + optional MiniLeaderboard
```

### WebSocket Events (Student Device)
```
← Receives from server:
  game_started       → show question screen (screen 2)
  question_revealed  → { question, choices, questionNumber, totalQuestions, timeLimit }
  answer_accepted    → { isCorrect, correctAnswer, pointsEarned }
  next_question      → { questionNumber } → show waiting screen (screen 5)
  game_over          → { finalRank, finalScore } → show final result

→ Sends to server:
  submit_answer      → { questionId, selectedChoice, timeTakenMs }
```

---

## Student Device — Display Only

Student devices show **questions and answer choices only**. There is no audio, music, video, or any media playback on student devices in any game mode. All sound and music plays exclusively on the teacher's desktop screen.
