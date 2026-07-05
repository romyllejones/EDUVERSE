# EduVerse — Full System Architecture

---

## Application Type

EduVerse is a **self-hosted, LAN-based desktop + mobile web application**:
- The **teacher's desktop** runs the application server (Node.js / Electron).
- **Student devices** connect via browser on the same WiFi network.
- No internet required during gameplay.
- Data persists locally on the teacher's machine (SQLite).

---

## High-Level Architecture

```
┌──────────────────────────────────────────────────────────────────┐
│                     TEACHER'S DESKTOP MACHINE                    │
│                                                                  │
│  ┌──────────────────────┐    ┌──────────────────────────────┐   │
│  │   Teacher UI         │    │   EduVerse Server            │   │
│  │   (React + Vite)     │◄──►│   (Node.js / Express)        │   │
│  │   Served at /        │    │   Port 3000                  │   │
│  └──────────────────────┘    │                              │   │
│                               │  ┌─────────────────────┐   │   │
│  ┌──────────────────────┐    │  │  SQLite Database     │   │   │
│  │   Student UI         │    │  │  (local file)        │   │   │
│  │   (React, mobile)    │◄──►│  │                      │   │   │
│  │   Served at /join    │    │  │  • users             │   │   │
│  └──────────────────────┘    │  │  • student_databases │   │   │
│                               │  │  • students          │   │   │
│                               │  │  • quizzes           │   │   │
│                               │  │  • questions         │   │   │
│                               │  │  • game_sessions     │   │   │
│                               │  │  • game_players      │   │   │
│                               │  │  • game_answers      │   │   │
│                               │  │  • sound_settings    │   │   │
│                               │  │  • app_settings      │   │   │
│                               │  └─────────────────────┘   │   │
│                               │                              │   │
│                               │  Socket.io (WebSocket)       │   │
│                               │  /teacher  /student          │   │
│                               └──────────────────────────────┘   │
└──────────────────────────────────────────────────────────────────┘
                               ▲
                   WiFi / LAN  │
                               ▼
┌─────────────────────────────────────────────────────────────────┐
│              STUDENT DEVICES (same WiFi, browser only)          │
│                                                                 │
│   Phone A         Phone B        Tablet C       Laptop D        │
│   192.168.x.x     192.168.x.x    192.168.x.x    192.168.x.x    │
│   Browser → http://192.168.1.10:3000/join?pin=A7B3K9           │
└─────────────────────────────────────────────────────────────────┘
```

---

## Module Map

```
EduVerse Application
├── Auth Module
│     ├── Teacher/Admin login (username + password)
│     └── Student login (Student ID + password, validated against uploaded DB)
│
├── Dashboard Module
│     └── Analytics charts (quiz play history, section performance)
│
├── Create Questions Module
│     ├── Multiple Choice (A/B/C/D, image attachments per choice)
│     ├── True or False
│     └── Type the Answer
│
├── Available Quizzes Module
│     ├── Quiz card grid
│     ├── Actions: Play Game, Assign Game, Review Mode, Duel Mode, Edit Quiz
│     └── Delete (with confirmation)
│
├── Games Module
│     ├── Classic Mode          (individual, speed+accuracy scoring)
│     ├── Accuracy Mode         (individual, accuracy-only scoring)
│     ├── Confidence Mode       (individual, answer+confidence level)
│     ├── The Lost Pyramid      (team-based, pyramid climb)
│     ├── Color Kingdom         (team-based, hex territory conquest)
│     └── Duel Mode             (1-vs-1 or team-vs-team)
│
├── Settings Module
│     ├── Account Settings
│     ├── Sound & Music         (per-game-mode audio upload per slot)
│     ├── Game Defaults
│     ├── Display & Theme
│     ├── Data & Storage
│     └── Network Settings      (port, IP display)
│
├── Students Database Module
│     ├── Multiple named database groups (classes)
│     ├── Per-student: ID Number, Thai Name, English Name, Section, Class Number
│     ├── CSV/XLSX bulk import
│     └── Inline table management
│
└── Reports Module
      ├── Game Session History
      ├── Section Scoreboard & Tally  ⭐
      ├── Student Performance
      └── Question Analysis
```

---

## Data Flow: Game Session (Full Cycle)

```
1. SETUP
Teacher selects Quiz + Game Mode
       ↓
Server creates game_session (status: lobby)
Generates Game PIN (6-char alphanumeric)
Generates join QR code URL

2. LOBBY
Students scan QR / type URL
Students log in (Student ID + password)
Server validates against students table
Creates game_player record
Socket joins room "game:PIN"
Teacher sees player tiles appear in real-time

3. GAMEPLAY (per question)
Teacher clicks START
Server changes status: question
Server emits question_revealed to all in room
Students see question + choices on device
Students submit answer → game_answer record created
Server tracks: is_correct, time_taken_ms, points_earned
Teacher screen shows live answer count

4. REVEAL
Timer ends or all answered
Server emits answer_reveal
Teacher screen shows correct answer + stats
Student devices show CORRECT / INCORRECT feedback

5. NEXT QUESTION
Teacher advances
Repeat steps 3–4 for each question

6. GAME OVER
Server calculates final rankings
Updates game_player.score and game_player.rank
Server emits game_over to all
Teacher sees final leaderboard
Students see final rank on their device
Session status → finished

7. REPORTS
All answers available immediately in Reports tab
Section scoreboard updates automatically
```

---

## Technology Stack

| Layer | Technology | Reason |
|-------|-----------|--------|
| Server runtime | Node.js v20+ | Cross-platform, runs on Windows/Mac/Linux |
| HTTP server | Express v5 | Lightweight, LAN-only |
| Real-time | Socket.io | WebSocket with LAN-friendly fallbacks |
| Local database | SQLite + Drizzle ORM | No separate DB server; single file |
| Teacher UI | React + Vite | Fast SPA, built and served by Express |
| Student UI | React (mobile-first) | Same codebase, different route/layout |
| Auth | bcrypt sessions | Local-only, no JWT cloud dependency |
| File storage | Local filesystem | Audio files, uploaded images stored locally |
| QR Code | `qrcode` npm package | Generated server-side, no external API |
| CSV parsing | `papaparse` | Browser-side CSV import |
| Audio | HTML5 Audio API | Plays stored .mp3/.wav/.ogg files |
| Packaging (optional) | Electron | Wraps as a desktop .exe/.app for easy distribution |

---

## Deployment Model

### Option A — Node.js Direct (Development / Tech-Savvy Schools)
```
teacher runs: node server.js
students open: http://[teacher-ip]:3000/join
```

### Option B — Electron Desktop App (Recommended for Schools)
```
teacher double-clicks: EduVerse.exe (or EduVerse.app on Mac)
App auto-starts server internally
Displays QR code and IP on screen
Students open browser and join
```

### Option C — Local Network Setup with Dedicated Machine
```
A spare PC or Raspberry Pi runs EduVerse server
Accessible at a fixed local IP (always-on within school)
Teachers access dashboard from their own browser
Students join as normal
```
