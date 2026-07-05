# EduVerse — Duel Mode (Student Device / Player View) UI Prompt

## Reference Image
`1_welcome_player.png`

---

## Visual Design Prompt

Same deep dark navy space theme. Portrait orientation. Two students are matched directly against each other.

---

### Screen 1 — Welcome Player (Joined)

Same welcome screen as all other modes:
- EduVerse glowing logo at top.
- "WELCOME PLAYER!" heading.
- Player info card (ID, English Name, Mathayum No, Section + Class).
- Game ID badge.
- Waiting animation + "WAITING FOR YOUR OPPONENT TO JOIN..."

Once both players are in, a brief "DUEL READY!" banner appears before the teacher starts.

---

### Screen 2 — Active Question (Duel)

Same question screen layout as Classic Mode (question card + ABCD choices).

**Key difference**: The student knows they're racing their opponent. UX communicates this:
- A small opponent indicator at the top (e.g., "vs. [Opponent Name]") with a subtle red/blue split.
- No explicit timer shown on student device (speed is hidden to reduce panic; teacher screen shows it).
- First tap locks the answer and auto-submits.

---

### Screen 3 — Round Result

After each question:
- CORRECT + WON ROUND: "✓ CORRECT — You won this round!" in green.
- CORRECT + LOST ROUND: "✓ Correct — But your opponent was faster." in amber.
- WRONG: "✗ INCORRECT" in red + correct answer shown.
- Running score update: "Score: 3 – 2" shown briefly.

---

### Screen 4 — Final Result (Duel Over)

- WON: Large "YOU WIN!" in bright gold + trophy illustration. Final score shown.
- LOST: "GOOD GAME" message + final score. Not demoralizing — encouraging tone.
- Optional "Play Again?" message (teacher controls if rematch is allowed).

---

## WebSocket Events (Student — Duel Mode)
```
← Receives:
  opponent_joined       → { opponentName, opponentSection }
  duel_started          → go to question screen
  question_revealed     → { question, choices, questionNumber }
  round_result          → { myResult, opponentResult, myScore, opponentScore }
  duel_over             → { winner: 'me'|'opponent', finalScore }

→ Sends:
  submit_answer         → { questionId, selectedChoice, timeTakenMs }
```
