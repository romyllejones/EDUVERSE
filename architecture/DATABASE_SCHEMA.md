# EduVerse — Full Database Schema

Database engine: **SQLite** (local file, no separate server)
ORM: **Drizzle ORM**

---

## Tables Overview

```
users
student_databases
students                 ← 5 required fields: id_number, thai_name, english_name, section, class_number
quizzes
quiz_parts
questions
answer_choices
game_sessions
game_players
game_answers
pyramid_teams            ← Lost Pyramid specific
hex_territories          ← Color Kingdom specific
sound_settings
app_settings
```

---

## users
```sql
CREATE TABLE users (
  id            TEXT PRIMARY KEY,            -- UUID
  username      TEXT UNIQUE NOT NULL,        -- Teacher ID (e.g. T852) or 'admin'
  password_hash TEXT NOT NULL,              -- bcrypt
  display_name  TEXT,
  role          TEXT NOT NULL DEFAULT 'teacher',  -- 'admin' | 'teacher'
  is_active     INTEGER DEFAULT 1,          -- 1=active, 0=inactive
  avatar_path   TEXT,                       -- local file path
  created_at    TEXT DEFAULT (datetime('now')),
  last_login    TEXT
);
```

---

## student_databases
```sql
CREATE TABLE student_databases (
  id          TEXT PRIMARY KEY,
  name        TEXT NOT NULL,               -- e.g. "Grade 10 – STEM 1"
  description TEXT,
  created_by  TEXT REFERENCES users(id),
  created_at  TEXT DEFAULT (datetime('now'))
);
```

---

## students
```sql
-- All 5 core fields in ONE table. Section is a text field — no separate section table.
CREATE TABLE students (
  id            TEXT PRIMARY KEY,
  database_id   TEXT NOT NULL REFERENCES student_databases(id) ON DELETE CASCADE,
  id_number     TEXT NOT NULL,             -- Login username, e.g. "2024-56789"
  thai_name     TEXT NOT NULL,             -- Thai full name, e.g. "นาย ฮวน เรเยส"
  english_name  TEXT NOT NULL,             -- English full name, e.g. "Juan Reyes"
  section       TEXT NOT NULL,             -- Section label, e.g. "STEM 1"
  class_number  INTEGER NOT NULL,          -- Seat/class number, e.g. 16
  password_hash TEXT NOT NULL,             -- bcrypt(id_number) by default
  is_active     INTEGER DEFAULT 1,
  created_at    TEXT DEFAULT (datetime('now')),
  UNIQUE(database_id, id_number)
);

CREATE INDEX idx_students_section ON students(database_id, section);
CREATE INDEX idx_students_id_number ON students(id_number);
```

---

## quizzes
```sql
CREATE TABLE quizzes (
  id          TEXT PRIMARY KEY,
  title       TEXT NOT NULL,
  subject     TEXT,
  created_by  TEXT REFERENCES users(id),
  created_at  TEXT DEFAULT (datetime('now')),
  updated_at  TEXT
);
```

---

## quiz_parts
```sql
CREATE TABLE quiz_parts (
  id        TEXT PRIMARY KEY,
  quiz_id   TEXT NOT NULL REFERENCES quizzes(id) ON DELETE CASCADE,
  part_num  INTEGER NOT NULL,               -- 1, 2, 3...
  exam_type TEXT NOT NULL                   -- 'multiple_choice' | 'true_false' | 'type_answer'
);
```

---

## questions
```sql
CREATE TABLE questions (
  id            TEXT PRIMARY KEY,
  quiz_part_id  TEXT NOT NULL REFERENCES quiz_parts(id) ON DELETE CASCADE,
  question_text TEXT NOT NULL,
  image_path    TEXT,                        -- optional question image
  correct_answer TEXT NOT NULL,              -- 'A'|'B'|'C'|'D' | 'TRUE'|'FALSE' | free text
  order_num     INTEGER NOT NULL DEFAULT 1
);
```

---

## answer_choices
```sql
-- Only for multiple_choice type
CREATE TABLE answer_choices (
  id            TEXT PRIMARY KEY,
  question_id   TEXT NOT NULL REFERENCES questions(id) ON DELETE CASCADE,
  choice_label  TEXT NOT NULL,               -- 'A', 'B', 'C', 'D'
  choice_text   TEXT NOT NULL,
  image_path    TEXT                         -- optional choice image
);
```

---

## game_sessions
```sql
CREATE TABLE game_sessions (
  id           TEXT PRIMARY KEY,
  quiz_id      TEXT REFERENCES quizzes(id),
  game_pin     TEXT UNIQUE NOT NULL,         -- 6-char alphanumeric
  game_mode    TEXT NOT NULL,                -- 'classic'|'accuracy'|'confidence'|'pyramid'|'colorkingdom'|'duel'
  status       TEXT DEFAULT 'lobby',         -- 'lobby'|'question'|'reveal'|'leaderboard'|'finished'
  created_by   TEXT REFERENCES users(id),
  database_id  TEXT REFERENCES student_databases(id),  -- which student group played
  config       TEXT,                         -- JSON blob for mode-specific settings
  started_at   TEXT,
  ended_at     TEXT,
  created_at   TEXT DEFAULT (datetime('now'))
);
```

---

## game_players
```sql
CREATE TABLE game_players (
  id          TEXT PRIMARY KEY,
  game_id     TEXT NOT NULL REFERENCES game_sessions(id) ON DELETE CASCADE,
  student_id  TEXT NOT NULL REFERENCES students(id),
  team_id     TEXT REFERENCES pyramid_teams(id),  -- NULL for individual modes
  score       INTEGER DEFAULT 0,
  rank        INTEGER,
  joined_at   TEXT DEFAULT (datetime('now'))
);
```

---

## game_answers
```sql
CREATE TABLE game_answers (
  id               TEXT PRIMARY KEY,
  game_id          TEXT NOT NULL REFERENCES game_sessions(id) ON DELETE CASCADE,
  player_id        TEXT NOT NULL REFERENCES game_players(id),
  question_id      TEXT NOT NULL REFERENCES questions(id),
  selected_answer  TEXT,                     -- 'A'|'B'|'C'|'D'|'TRUE'|'FALSE'|free text
  is_correct       INTEGER,                  -- 1|0
  time_taken_ms    INTEGER,
  points_earned    INTEGER DEFAULT 0,
  confidence_level INTEGER,                  -- 1|2|3, only for confidence mode
  answered_at      TEXT DEFAULT (datetime('now'))
);

CREATE INDEX idx_answers_game ON game_answers(game_id);
CREATE INDEX idx_answers_player ON game_answers(player_id);
```

---

## pyramid_teams
```sql
-- The Lost Pyramid mode
CREATE TABLE pyramid_teams (
  id            TEXT PRIMARY KEY,
  game_id       TEXT NOT NULL REFERENCES game_sessions(id) ON DELETE CASCADE,
  team_number   INTEGER NOT NULL,            -- 1, 2, 3, 4
  team_name     TEXT,                        -- auto-assigned: Anubis, Pharaohs, Mummies, Horus
  character     TEXT,                        -- 'anubis'|'pharaoh'|'mummy'|'horus'
  current_step  INTEGER DEFAULT 1,
  color         TEXT                         -- hex color code for team
);
```

---

## hex_territories
```sql
-- Color Kingdom mode
CREATE TABLE hex_territories (
  id          TEXT PRIMARY KEY,
  game_id     TEXT NOT NULL REFERENCES game_sessions(id) ON DELETE CASCADE,
  hex_row     INTEGER NOT NULL,
  hex_col     INTEGER NOT NULL,
  owner_team  TEXT REFERENCES pyramid_teams(id),   -- NULL = neutral
  captured_at TEXT,
  UNIQUE(game_id, hex_row, hex_col)
);
```

---

## sound_settings
```sql
CREATE TABLE sound_settings (
  id            TEXT PRIMARY KEY,
  user_id       TEXT REFERENCES users(id),
  game_mode     TEXT NOT NULL,               -- 'classic'|'accuracy'|'confidence'|'pyramid'|'colorkingdom'|'duel'
  slot_key      TEXT NOT NULL,               -- e.g. 'lobby_music', 'player_join', 'correct_answer'
  file_path     TEXT,                        -- NULL = use factory default
  original_name TEXT,
  file_size_kb  INTEGER,
  uploaded_at   TEXT DEFAULT (datetime('now')),
  UNIQUE(user_id, game_mode, slot_key)
);
```

---

## app_settings
```sql
CREATE TABLE app_settings (
  id             TEXT PRIMARY KEY,
  user_id        TEXT UNIQUE REFERENCES users(id),
  master_volume  INTEGER DEFAULT 80,         -- 0–100
  is_muted       INTEGER DEFAULT 0,
  game_defaults  TEXT,                       -- JSON: { defaultTimer, minPlayers, stepsToWin, ... }
  display_prefs  TEXT,                       -- JSON: { theme, sidebarBehavior, fontScale, ... }
  network_prefs  TEXT,                       -- JSON: { port, manualIp }
  updated_at     TEXT DEFAULT (datetime('now'))
);
```

---

## Section Score Tally — Query

```sql
-- Get section-level performance summary for reports
SELECT
  s.section,
  s.database_id,
  COUNT(DISTINCT s.id)                                               AS student_count,
  COUNT(DISTINCT gs.id)                                              AS games_played,
  SUM(CASE WHEN ga.is_correct = 1 THEN 1 ELSE 0 END)               AS total_correct,
  COUNT(ga.id)                                                        AS total_answers,
  ROUND(AVG(gp.score), 0)                                           AS avg_score,
  ROUND(
    CAST(SUM(CASE WHEN ga.is_correct = 1 THEN 1 ELSE 0 END) AS REAL)
    / MAX(COUNT(ga.id), 1) * 100, 1
  )                                                                   AS accuracy_percent
FROM game_answers ga
JOIN game_players  gp ON gp.id = ga.player_id
JOIN students       s ON s.id  = gp.student_id
JOIN game_sessions gs ON gs.id = ga.game_id
WHERE gs.status = 'finished'
GROUP BY s.section, s.database_id
ORDER BY avg_score DESC;
```

---

## Default Seed Data

```sql
-- Admin / developer account (seeded on first launch)
INSERT OR IGNORE INTO users (id, username, password_hash, display_name, role)
VALUES (
  'seed-admin-001',
  'admin',
  '<bcrypt of admin123>',
  'Administrator',
  'admin'
);

-- Default app settings for admin
INSERT OR IGNORE INTO app_settings (id, user_id, master_volume, is_muted)
VALUES ('settings-admin-001', 'seed-admin-001', 80, 0);
```
