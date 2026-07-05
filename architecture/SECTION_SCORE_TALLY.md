# EduVerse — Section Score Tally System

---

## Purpose

The Section Score Tally tracks and displays **cumulative performance per class section** across all game sessions. It is the primary reporting tool for teachers who want to compare how different sections are doing.

---

## Data Model

Scores are NOT stored in a separate tally table. They are computed **on-demand** from raw game answer data, aggregated by the `section` field in the `students` table.

### Key Relationships
```
students.section  ──►  grouped with ──►  game_answers  ──►  aggregated into section stats
```

---

## What Gets Tallied Per Section

| Metric | How Calculated |
|--------|---------------|
| **Games Played** | COUNT DISTINCT game_session_ids where at least one student from this section participated |
| **Total Students** | COUNT DISTINCT student_ids with this section value |
| **Total Answers Submitted** | COUNT all game_answer rows |
| **Total Correct Answers** | COUNT rows where is_correct = 1 |
| **Total Wrong Answers** | COUNT rows where is_correct = 0 |
| **Accuracy %** | (Total Correct / Total Answers) × 100 |
| **Average Score** | AVG of game_player.score per game session, per student in section |
| **Top Student** | Student with highest total score across all games |
| **Best Game Mode** | Game mode where this section had highest accuracy |

---

## Tally Scope Options (Filters)

The tally can be scoped by:
- **Date Range**: This week / This month / Custom range.
- **Quiz**: Only count sessions using a specific quiz.
- **Game Mode**: Only count sessions of a specific mode.
- **Student Database**: Only count students from a specific database group.
- **All** (default): No filters — entire history.

---

## Reports → Section Scoreboard UI

### Section Summary Cards (top of page)

```
┌───────────────────────────────────────────────┐
│  Grade 10 — STEM 1                            │
│  ─────────────────────────────────────────    │
│  👥 28 students                               │
│  🎮 Games Played: 12                          │
│  ✅ Total Correct: 842  ❌ Wrong: 216          │
│  📈 Accuracy: 79.6%                           │
│  🏆 Avg Score: 734 pts/game                   │
│  ⭐ Top Student: Juan Reyes (2024-56789)       │
└───────────────────────────────────────────────┘
```

One card per section. Cards sorted by average score descending (highest section first).

---

### Section Comparison Bar Chart

Horizontal or vertical bar chart:
- X-axis: Section names (e.g., STEM 1, Arts 2, Science A).
- Y-axis: Average score.
- Bars color-coded (one color per section, consistent).
- Hover tooltip: Section name + all stats.
- Second dataset toggle: switch between "Average Score" and "Accuracy %" views.

---

### Section Leaderboard Table

Full sortable table below the bar chart:

| # | Section | Database | Students | Games | Correct | Accuracy | Avg Score | Top Student |
|---|---------|----------|----------|-------|---------|----------|-----------|-------------|
| 1 | STEM 1 | Grade 10 | 28 | 12 | 842 | 79.6% | 734 | Juan Reyes |
| 2 | Arts 2 | Grade 10 | 24 | 10 | 640 | 71.1% | 680 | Maria Santos |
| 3 | Science A | Grade 9 | 30 | 8 | 590 | 65.0% | 612 | Alex Johnson |

- Sortable by any column (click header).
- Click any row → drill-down view for that section.

---

### Section Drill-Down View

Opens when clicking a section row. Shows:

**Header**: Section name + database group + total students.

**Per-Student Table:**

| # | Class No | ID Number | Thai Name | English Name | Games | Correct | Wrong | Accuracy | Avg Score |
|---|----------|-----------|-----------|--------------|-------|---------|-------|----------|-----------|
| 1 | 16 | 2024-56789 | นาย ฮวน เรเยส | Juan Reyes | 12 | 98 | 12 | 89% | 890 |
| 2 | 17 | 2024-56790 | นางสาว มาเรีย | Maria Santos | 12 | 85 | 25 | 77% | 742 |

- Sorted by Avg Score descending by default.
- Search by name or ID.
- **Export this section** button: downloads this section's data as CSV.

**Question Heatmap (below table):**

A grid showing which questions this section answered correctly most/least:
- Rows = questions (labeled by question number + short text).
- Color = % correct (green = high, yellow = medium, red = low).
- Hover: exact % + question text.

---

## Game-by-Game Tally View (Per Section)

Teacher can see the section's score evolution over time:

```
Game Date      | Quiz Name         | Mode      | Avg Score | Accuracy | Top Scorer
───────────────────────────────────────────────────────────────────────────────────
May 10, 2026   | Math Quiz         | Classic   | 820 pts   | 84%      | Juan (950)
May 12, 2026   | Science Quiz      | Accuracy  | 740 pts   | 76%      | Maria (800)
May 15, 2026   | History Quiz      | Pyramid   | 680 pts   | 70%      | Alex (Team1)
```

---

## Student Score Card (Individual, within Section context)

When drilling into an individual student from the section view:

```
┌─────────────────────────────────────────────┐
│  👤 Juan Miguel D. Reyes                    │
│  ID: 2024-56789   Class No: 16              │
│  Thai: นาย ฮวน เรเยส                        │
│  Section: STEM 1  •  Grade 10               │
│                                             │
│  📊 PERFORMANCE SUMMARY                     │
│  Games Played:    12                        │
│  Total Score:     9,480 pts                 │
│  Avg Score/Game:  790 pts                   │
│  Overall Correct: 89 / 110 answers          │
│  Overall Accuracy: 80.9%                    │
│  Best Mode:       Confidence Mode (91%)     │
│                                             │
│  📈 SCORE TREND (line chart)                │
│  [Game 1: 820] [Game 2: 750] [Game 3: 910]  │
│   ↗ Improving over time                    │
└─────────────────────────────────────────────┘
```

---

## Export Formats

### Section Scoreboard CSV Export
```csv
section,database_name,student_count,games_played,total_correct,total_wrong,accuracy_percent,avg_score,top_student_id,top_student_name
STEM 1,Grade 10,28,12,842,216,79.6,734,2024-56789,Juan Miguel D. Reyes
Arts 2,Grade 10,24,10,640,260,71.1,680,2024-56791,Maria Santos
```

### Student Scores CSV Export (per section)
```csv
class_number,id_number,thai_name,english_name,section,games_played,total_correct,total_wrong,accuracy_percent,avg_score
16,2024-56789,นาย ฮวน เรเยส,Juan Miguel D. Reyes,STEM 1,12,98,12,89.1,890
17,2024-56790,นางสาว มาเรีย ซานโตส,Maria Santos,STEM 1,12,85,25,77.3,742
```

---

## Backend Implementation

### Endpoint
```
GET /api/reports/sections
Query params:
  databaseId?  (filter to one database group)
  quizId?      (filter to one quiz)
  mode?        (filter to one game mode)
  from?        (ISO date string)
  to?          (ISO date string)

Response:
{
  sections: [
    {
      section: "STEM 1",
      databaseId: "uuid",
      databaseName: "Grade 10",
      studentCount: 28,
      gamesPlayed: 12,
      totalCorrect: 842,
      totalWrong: 216,
      totalAnswers: 1058,
      accuracyPercent: 79.6,
      avgScore: 734,
      topStudent: {
        idNumber: "2024-56789",
        englishName: "Juan Miguel D. Reyes",
        thaiName: "นาย ฮวน เรเยส",
        totalScore: 9480
      }
    }
  ],
  generatedAt: "2026-07-03T10:00:00Z"
}
```

### SQL Query (SQLite)
```sql
SELECT
  s.section,
  s.database_id,
  sd.name                                                          AS database_name,
  COUNT(DISTINCT s.id)                                             AS student_count,
  COUNT(DISTINCT gs.id)                                            AS games_played,
  SUM(CASE WHEN ga.is_correct = 1 THEN 1 ELSE 0 END)             AS total_correct,
  SUM(CASE WHEN ga.is_correct = 0 THEN 1 ELSE 0 END)             AS total_wrong,
  COUNT(ga.id)                                                      AS total_answers,
  ROUND(
    CAST(SUM(CASE WHEN ga.is_correct = 1 THEN 1 ELSE 0 END) AS REAL)
    / MAX(COUNT(ga.id), 1) * 100, 1
  )                                                                 AS accuracy_percent,
  ROUND(AVG(gp.score), 0)                                         AS avg_score
FROM game_answers ga
JOIN game_players  gp  ON gp.id         = ga.player_id
JOIN students      s   ON s.id          = gp.student_id
JOIN student_databases sd ON sd.id      = s.database_id
JOIN game_sessions gs  ON gs.id         = ga.game_id
WHERE gs.status = 'finished'
  AND ((:databaseId IS NULL) OR s.database_id = :databaseId)
  AND ((:quizId     IS NULL) OR gs.quiz_id    = :quizId)
  AND ((:mode       IS NULL) OR gs.game_mode  = :mode)
  AND ((:from       IS NULL) OR gs.started_at >= :from)
  AND ((:to         IS NULL) OR gs.started_at <= :to)
GROUP BY s.section, s.database_id, sd.name
ORDER BY avg_score DESC;
```
