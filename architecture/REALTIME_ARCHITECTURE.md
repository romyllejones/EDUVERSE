# EduVerse — Real-Time Architecture (Socket.io / LAN WebSocket)

---

## Overview

All live game communication uses **Socket.io** running over the local WiFi network. This enables:
- Real-time player join notifications.
- Instant question broadcasts to all student devices.
- Live answer progress counters.
- Synchronized score updates.
- Game state changes visible simultaneously on teacher screen + all student devices.

---

## Connection Setup

### Teacher Connection
```js
// Teacher browser connects to:
const socket = io('http://localhost:3000', {
  auth: { role: 'teacher', token: teacherSessionToken }
});
socket.emit('game:host', { gameId });
```

### Student Connection
```js
// Student browser connects via local IP:
const socket = io('http://192.168.1.10:3000', {
  auth: { role: 'student', token: studentSessionToken }
});
socket.emit('game:join', { gamePin, studentId });
```

---

## Room Architecture

```
Socket.io Server
└── Room: "game:{gamePin}"          ← e.g. "game:A7B3K9"
      ├── Teacher socket (1)
      └── Student sockets (N)       ← up to ~60 concurrent on LAN
```

All broadcasts within a game use `io.to("game:A7B3K9").emit(...)`.

---

## Complete Event Flow Diagram

```
TEACHER                    SERVER                    STUDENTS
   │                          │                          │
   │── game:start ──────────►│                          │
   │                          │── game:started ─────────►│ (all students)
   │                          │                          │
   │                          │── question:announce ────►│ (question N incoming)
   │◄── question:announce ───│                          │
   │                          │                          │
   │                          │── question:active ──────►│ (question + choices)
   │◄── question:active ─────│                          │
   │                          │                          │
   │                          │◄── answer:submit ────────│ (student A)
   │◄── answer:progress ─────│ { answered:1, total:28 } │
   │                          │◄── answer:submit ────────│ (student B)
   │◄── answer:progress ─────│ { answered:2, total:28 } │
   │                          │    ...                   │
   │                          │                          │
   │── game:next ────────────►│ (teacher clicks reveal)  │
   │                          │── answer:reveal ─────────►│
   │◄── answer:reveal ────────│                          │
   │                          │── score:update ──────────►│
   │◄── score:update ─────────│                          │
   │                          │                          │
   │   [repeat for each question]                        │
   │                          │                          │
   │── game:end ─────────────►│                          │
   │                          │── game:over ─────────────►│
   │◄── game:over ────────────│                          │
```

---

## Handling Disconnects

### Student Disconnects Mid-Game
```
on('disconnect', student):
  → mark game_player as disconnected (not removed)
  → student can reconnect within 60s using same session token
  → if reconnects: server sends current game state to re-sync
  → if does not reconnect: treated as no-answer for remaining questions
  → teacher sees "(disconnected)" badge on player tile
```

### Teacher Disconnects
```
on('disconnect', teacher):
  → game paused (students see "Waiting for host...")
  → if teacher reconnects within 120s: game resumes
  → if not: game saved as 'interrupted', partial data available in Reports
```

---

## Latency Optimization (LAN Context)

Since all devices are on the same WiFi:
- No need for message queuing or offline sync.
- WebSocket messages arrive in < 50ms typically.
- Answer timestamps are recorded **server-side** (not trusted from client) to prevent cheating.
- Timer is authoritative on the server: `question_active` event includes `{ startsAt: serverTimestamp, duration: 30000 }`. Client renders countdown from this, but server ends the question based on its own clock.

---

## Server-Side Timer Logic

```js
class QuestionTimer {
  constructor(durationMs, onExpire) {
    this.timer = setTimeout(onExpire, durationMs)
    this.startedAt = Date.now()
  }

  cancel() { clearTimeout(this.timer) }

  remaining() { return Math.max(0, this.durationMs - (Date.now() - this.startedAt)) }
}

// When teacher starts a question:
const timer = new QuestionTimer(30_000, () => {
  gameController.endQuestion(gameId)      // server-authoritative end
  io.to(room).emit('answer:reveal', { ... })
})
```

---

## Canonical Event Name Reference

> **This table is the single source of truth for all event names.**
> Some per-mode and student-device prompt files use shorthand aliases in their narrative sections; the canonical Socket.io event names below are what must be used in implementation.

### Server → Client (Broadcast)

| Canonical Event | Alias / Short Name | Payload Summary |
|---|---|---|
| `game:started` | `game_started` | `{ gameId, pin, mode, questionCount }` |
| `question:announce` | `question_start` | `{ questionNumber, total }` — heads-up before question |
| `question:active` | `question_revealed` | `{ questionId, questionText, choices, timeLimit }` |
| `answer:progress` | — | `{ answered, total }` — teacher only |
| `answer:reveal` | `answer_accepted`, `correct_reveal` | `{ correctChoiceId, isCorrect, pointsEarned, leaderboard }` |
| `score:update` | `leaderboard_update`, `score_update` | `{ leaderboard: [{rank, studentId, name, score}] }` |
| `game:over` | `game_over` | `{ finalLeaderboard, gameId }` |
| `lobby:player_joined` | `player_joined` | `{ student: {id, name, section} }` |
| `lobby:player_left` | `player_left` | `{ studentId }` |
| `team:update` | — | `{ teams: [{id, name, members, step/hexCount}] }` — team modes |
| `hex:update` | — | `{ hexMap }` — Color Kingdom only |

### Client → Server

| Canonical Event | Alias / Short Name | Payload Summary |
|---|---|---|
| `game:join` | — | `{ gamePin, studentId, studentToken }` |
| `game:host` | — | `{ gameId }` — teacher only |
| `game:start` | — | `{ gameId }` — teacher only |
| `game:next` | — | `{ gameId }` — teacher advances to reveal |
| `game:end` | — | `{ gameId }` — teacher force-ends |
| `answer:submit` | `submit_answer` | `{ questionId, answer, timeTakenMs, confidenceLevel? }` |
| `team:join` | — | `{ gameId, teamId }` — team modes |

> **Implementation note:** When implementing Socket.io listeners, register handlers under the **canonical** event names only. The alias column is for cross-referencing legacy mentions in older per-mode docs.

---

## Concurrent Game Support

Multiple teachers can run simultaneous games on the same server:
- Each game is isolated in its own Socket.io room.
- Game PINs are globally unique.
- SQLite handles concurrent reads well; writes are serialized automatically.
- Recommended max: 5 simultaneous games on a single machine (limited by WiFi bandwidth).

---

## Network Display on Teacher Dashboard

The teacher dashboard prominently shows:
```
┌────────────────────────────────────┐
│  Your Local Address                │
│  http://192.168.1.10:3000/join     │
│  [QR Code]                         │
│  Students: same WiFi required      │
└────────────────────────────────────┘
```

The server auto-detects the local IP on startup using Node.js `os.networkInterfaces()`. If multiple interfaces are detected, the teacher can manually select the correct one in Settings → Network.
