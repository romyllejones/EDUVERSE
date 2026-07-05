# EduVerse — Sound Upload Quick Reference Guide

> This is a companion to `SETTINGS_PROMPT.md`. Use this as a quick reference when building the Sound & Music upload UI.

---

## Complete Sound Slot Reference (All Game Modes)

### 🏆 CLASSIC MODE

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_music` | Lobby Background Music | While waiting for players | ✅ Loop | 30–120s |
| `player_join` | Player Join Sound | Each student joins | ❌ | 0.5–1s |
| `game_start` | Game Start Sound | Teacher clicks Start | ❌ | 2–4s |
| `question_reveal` | Question Reveal | New question appears | ❌ | 1–2s |
| `timer_tick` | Timer Countdown | Last 5 seconds | ❌ (plays 5×) | 1s |
| `time_up` | Time's Up | Timer hits 0 | ❌ | 1–2s |
| `correct_reveal` | Correct Answer Reveal | Correct shown | ❌ | 1–2s |
| `wrong_reveal` | Wrong Answer Reveal | Wrong shown | ❌ | 1–2s |
| `leaderboard_music` | Leaderboard Music | Leaderboard screen | ✅ Loop | 30–60s |
| `winner_sound` | Winner Announcement | 1st place shown | ❌ | 3–5s |

---

### 🎯 ACCURACY MODE

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_music` | Lobby Background Music | Waiting | ✅ Loop | 30–120s |
| `player_join` | Player Join Sound | Student joins | ❌ | 0.5–1s |
| `game_start` | Game Start Sound | Game begins | ❌ | 2–4s |
| `question_reveal` | Question Reveal | Question appears | ❌ | 1–2s |
| `all_answered` | All Players Answered | Everyone submitted | ❌ | 0.5–1s |
| `correct_reveal` | Correct Answer | Reveal correct | ❌ | 1–2s |
| `wrong_reveal` | Wrong Answer | Reveal wrong | ❌ | 1–2s |
| `streak_bonus` | Streak Bonus | 3 consecutive correct | ❌ | 1–2s |
| `leaderboard_music` | Leaderboard Music | End screen | ✅ Loop | 30–60s |

---

### 🧠 CONFIDENCE MODE

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_music` | Lobby Background Music | Waiting | ✅ Loop | 30–120s |
| `player_join` | Player Join Sound | Student joins | ❌ | 0.5–1s |
| `game_start` | Game Start Sound | Game begins | ❌ | 2–4s |
| `question_reveal` | Question Reveal | Question appears | ❌ | 1–2s |
| `conf_high_correct` | High Confidence Correct | L3 + correct | ❌ | 2–3s |
| `conf_high_wrong` | High Confidence Wrong | L3 + wrong | ❌ | 2–3s |
| `conf_med_correct` | Medium Confidence Correct | L2 + correct | ❌ | 1–2s |
| `conf_med_wrong` | Medium Confidence Wrong | L2 + wrong | ❌ | 1–2s |
| `conf_low_correct` | Low Confidence Correct | L1 + correct | ❌ | 1s |
| `conf_low_wrong` | Low Confidence Wrong | L1 + wrong | ❌ | 1s |
| `answer_reveal` | Answer Reveal | Correct answer shown | ❌ | 1–2s |
| `leaderboard_music` | Leaderboard Music | End screen | ✅ Loop | 30–60s |

---

### 🏛 THE LOST PYRAMID

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_music` | Lobby Background Music | Waiting | ✅ Loop | 30–120s |
| `player_join` | Player Join Sound | Student joins | ❌ | 0.5–1s |
| `team_reveal_music` | Team Reveal Music | Team preview screens | ✅ Loop (short) | 10–20s |
| `game_start_fanfare` | Game Start Fanfare | Welcome to Pyramid screen | ❌ | 4–6s |
| `question_announce` | Question Announce | "QUESTION N" screen | ❌ | 1–3s |
| `question_active_music` | Question Active Music | During answer phase | ✅ Loop | 20–40s |
| `timer_tick` | Timer Countdown | Last 5 seconds | ❌ (×5) | 1s |
| `time_up_gong` | Time's Up Gong | Timer hits 0 | ❌ | 2–3s |
| `team_correct` | Team Correct Answer | Team advances | ❌ | 2–3s |
| `team_wrong` | Team Wrong Answer | Team held/falls | ❌ | 1–2s |
| `team_advances` | Team Advances (step move) | Avatar moves up | ❌ | 1–2s |
| `team_falls` | Team Falls Back | Avatar moves down | ❌ | 1–2s |
| `reach_top` | One Team Reaches Top | First to step 15 | ❌ | 4–6s |
| `winner_anthem` | Winner Proclamation Music | Final screen | ✅ Loop | 30–60s |

---

### 👑 COLOR KINGDOM

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_music` | Lobby Background Music | Waiting | ✅ Loop | 30–120s |
| `player_join` | Player Join Sound | Student joins | ❌ | 0.5–1s |
| `team_select_music` | Team Selection Music | "Choose Your Team" | ✅ Loop | 20–40s |
| `game_start_fanfare` | Game Start Fanfare | "Welcome to Color Kingdom" | ❌ | 4–6s |
| `question_reveal` | Question Reveal | Question appears | ❌ | 1–2s |
| `team_correct` | Team Correct Answer | Team wins question | ❌ | 1–2s |
| `team_wrong` | Team Wrong Answer | Team loses round | ❌ | 1s |
| `hex_capture` | Hex Capture Sound | Hexes being claimed | ❌ | 0.5–1s |
| `territory_battle` | Territory Battle | Contested hex fight | ❌ | 1–2s |
| `win_condition` | Win Condition Met | Hex dominance reached | ❌ | 3–4s |
| `winner_anthem` | Winner Proclamation Music | Final screen | ✅ Loop | 30–60s |

---

### ⚔ DUEL MODE

| Slot Key | Label | When It Plays | Loop? | Rec. Duration |
|----------|-------|--------------|-------|--------------|
| `lobby_tension` | Lobby Tension Music | Waiting for opponent | ✅ Loop | 30–60s |
| `p1_joins` | Player 1 Joins | First player connects | ❌ | 1s |
| `p2_joins` | Player 2 Joins | Second player connects | ❌ | 1s |
| `duel_start` | Duel Start Sound | Teacher launches duel | ❌ | 2–4s |
| `question_reveal` | Question Reveal | Question appears | ❌ | 0.5–1s |
| `player_buzzes` | Player Buzzes In | Either player answers | ❌ | 0.3–0.5s |
| `p1_round_win` | Player 1 Round Win | P1 wins a round | ❌ | 1–2s |
| `p2_round_win` | Player 2 Round Win | P2 wins a round | ❌ | 1–2s |
| `both_wrong` | Both Wrong | Neither correct | ❌ | 1s |
| `winner_fanfare` | Final Winner Fanfare | Duel champion revealed | ❌ | 4–6s |

---

### 📱 STUDENT DEVICES — No Audio

> **Student devices have no audio, music, or video of any kind — in all game modes, including Duel Mode and Color Kingdom.**
> All sound slots listed above play only on the teacher's desktop screen. No audio is sent to or played on student devices. The Settings panel must NOT show any student-side sound upload section.

---

## UI Behavior Rules for Upload Panel

1. **Only one file picker open at a time** — clicking Upload on one slot auto-closes any other open picker.
2. **File validation on select** (before upload):
   - Check file extension (`.mp3`, `.wav`, `.ogg`, `.m4a`).
   - Check file size (max 10 MB).
   - If invalid: show inline error in red "Unsupported format" or "File too large".
3. **Upload progress** — show a small progress bar during upload.
4. **Preview button** — plays a 3-second preview of the current sound (default or custom). Stop button appears while playing.
5. **Remove button** — shows only when a custom file has been uploaded. Clicking removes it and reverts to factory default. Shows confirmation: "Remove custom sound and use default?"
6. **Unsaved changes indicator** — a yellow dot next to the section header if unsaved slot changes exist. "Save Changes" button at bottom of each accordion section.
7. **After save** — show toast: "✓ Sound settings saved."
