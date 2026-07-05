# EduVerse — Accuracy Mode (Student Device / Player View) UI Prompt

## Reference Images (in order)
1. `1_welcome_player.png`
2. `2_reveal_question.png`
3. `3_wating_for_correct_anwer.png`
4. `4_answer_if_correct.png`
5. `4_answer_if_wrong.png`
6. `5_waiting_for_next_question.png`

---

## Visual Design Prompt

**Identical visual design to Classic Mode student screens.** The UI template is shared — the same dark navy space theme, game ID badge, question card, and ABCD choice buttons apply.

The only difference is **messaging and feedback** — Accuracy Mode communicates to students that speed does NOT matter and accuracy DOES.

---

### Screen 1 — Welcome Player
Same as Classic Mode welcome screen. Shows player info card + waiting animation.

> Optionally: a small "ACCURACY MODE" badge/label on the welcome card to orient the student.

---

### Screen 2 — Question Reveal

Same layout as Classic Mode:
- Question counter ("QUESTION X OF Y").
- Question text card.
- ABCD answer choice buttons (same color scheme).

**Key UX difference**: No visible countdown timer displayed by default (teacher may enable one in settings). The UI should feel **calm and deliberate** — not rushed.

Optional hint text below answer buttons: *"Take your time — accuracy matters more than speed."* (shown on first question only, fades after 3s).

---

### Screen 3 — Answer Submitted / Waiting

Same as Classic Mode — selected button highlighted, waiting for reveal.

---

### Screen 4a — Correct Answer

Same as Classic Mode: green checkmark overlay + "CORRECT!" + optional "+100 pts" score animation.

Streak indicator: if student has 3 consecutive correct answers, show a "🔥 3-Streak Bonus!" banner below the checkmark.

---

### Screen 4b — Wrong Answer

Same as Classic Mode: red X + "INCORRECT" + correct answer highlight.

---

### Screen 5 — Waiting for Next Question

Same as Classic Mode. Optionally shows current score and streak count.

---

## Frontend Architecture (Mobile)

Reuses the same `QuestionScreen`, `AnswerButtons`, `ResultOverlay` components from Classic Mode. The only difference is the `gameMode` prop passed to the view, which controls:
- Whether a timer is shown.
- Whether streak UI is shown.
- Score display format (flat +100 vs time-adjusted).

```
AccuracyModeStudentView extends ClassicModeStudentView
  → showTimer: false (unless teacher enabled)
  → showStreak: true
  → scoringType: "accuracy"
```

---

## WebSocket Events (Student Device — Accuracy Mode)
```
← Receives:
  question_revealed  → { question, choices, questionNumber, totalQuestions, timeLimitOptional }
  answer_accepted    → { isCorrect, correctAnswer, pointsEarned, streakCount }
  all_answered       → broadcast when every student has answered
  game_over          → { finalRank, finalScore, accuracy: "87%" }

→ Sends:
  submit_answer      → { questionId, selectedChoice, timeTakenMs }
```
