# EduVerse — Confidence Mode (Student Device / Player View) UI Prompt

## Reference Images (in order)
1. `1_welcome_player.png`
2. `2_reveal_question.png`
3. `3_choose_confidence_level.png`
4. `4_answer_if_correct.png`
5. `4_answer_if_wrong.png`
6. `5_wating_for_correct_anwer.png`
7. `6_waiting_for_next_question.png`

---

## Visual Design Prompt

Same deep dark navy space theme. Portrait orientation. Shares the core question/answer layout with Classic Mode but adds a unique **Confidence Level selection step**.

---

### Screen 1 — Welcome Player
Same as Classic Mode welcome screen.

---

### Screen 2 — Question Reveal

Same as Classic Mode:
- Question counter at top.
- Question text card.
- ABCD answer choice buttons (color coded).

Student selects their answer **first** before seeing the confidence selector.

---

### Screen 3 — Choose Confidence Level ⭐ (UNIQUE TO THIS MODE)

After selecting an answer, this screen appears **without navigating away** — it slides up as a second panel below the locked answer choices, or replaces the bottom half of the screen.

**Confidence Level Section Layout**:
- Separator label: "— CONFIDENCE LEVEL —" in small cyan caps, centered, with decorative dashes on both sides.
- Three confidence option cards in a 3-column row:

| Card | Icon | Number | Label |
|------|------|--------|-------|
| Level 1 | 😊 (1 smiley) | **1** | "Low Confidence" |
| Level 2 | 😊😊 (2 smileys) | **2** | "Medium Confidence" |
| Level 3 | 😊😊😊 (3 smileys) | **3** | "High Confidence" |

Each card:
- Dark rounded card with blue border.
- Emoji icon(s) at top center (large).
- Bold number below.
- Small label text at bottom.
- Tap to select → card highlights with cyan glow border, others dim.

**SELECT button** (below the confidence cards):
- Full-width, dark gray/muted button with "SELECT" text.
- Becomes active (blue fill) once a confidence level is tapped.
- Tapping SELECT submits both the answer and confidence level.

---

### Screen 4a — Correct Answer

Same as Classic Mode but also shows:
- Points earned based on confidence:
  - Level 1 correct: "+100 pts"
  - Level 2 correct: "+200 pts"
  - Level 3 correct: "+300 pts" (with a special "HIGH CONFIDENCE BONUS!" text)

---

### Screen 4b — Wrong Answer

Same X overlay but also shows penalty:
- Level 1 wrong: "-50 pts"
- Level 2 wrong: "-100 pts"
- Level 3 wrong: "-150 pts" (with "HIGH CONFIDENCE PENALTY" text — teaches metacognition)

---

### Screens 5–6 — Waiting for Reveal / Waiting for Next Question

Same as Classic Mode. Optionally shows current confidence accuracy stats.

---

## Frontend Architecture (Mobile)

```
ConfidenceModeStudentView
  ├── WelcomeScreen (same as Classic)
  ├── QuestionScreen
  │     ├── QuestionCounter
  │     ├── QuestionCard
  │     └── AnswerButtons[] (A, B, C, D)
  ├── ConfidenceSelectorPanel (slides in after answer tap)
  │     ├── ConfidenceLevelLabel ("— CONFIDENCE LEVEL —")
  │     ├── ConfidenceOptionCards[]
  │     │     ├── EmojiIcon
  │     │     ├── LevelNumber
  │     │     └── LevelLabel
  │     └── SelectButton (disabled until confidence chosen)
  ├── AnswerLockedScreen (waiting for reveal)
  ├── ResultOverlay
  │     ├── CorrectOverlay (checkmark + "CORRECT" + "+X pts" + confidence label)
  │     └── WrongOverlay (X + "INCORRECT" + "-X pts" + correct answer)
  └── WaitingNextScreen
```

### State Machine
```
"question_active"
    → user taps answer → selectedAnswer set
    → "confidence_required"
        → user taps confidence level → confidenceLevel set
        → user taps SELECT → submit_answer event sent
        → "waiting_for_reveal"
            → server reveals answer
            → "showing_result"
                → auto-advance after 3s
                → "waiting_next_question"
```

---

## WebSocket Events (Student Device — Confidence Mode)
```
← Receives:
  question_revealed   → { question, choices, questionNumber, totalQuestions }
  answer_accepted     → { isCorrect, correctAnswer, pointsEarned, penaltyApplied, confidenceLevel }
  game_over           → { finalRank, finalScore, confidenceAccuracy: "X%" }

→ Sends:
  submit_answer       → { questionId, selectedChoice, confidenceLevel: 1|2|3, timeTakenMs }
```
