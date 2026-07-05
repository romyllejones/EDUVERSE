# EduVerse — Reports & Score Tally (Desktop / Teacher View) UI Prompt

---

## Overview

The **Reports** module gives teachers a complete view of **student and section performance** across all game sessions. It is the primary tool for tracking who answered what, how sections compare, and who the top performers are.

---

## Visual Design Prompt

### Layout
- Same dashboard layout: fixed left sidebar + top header + scrollable main content.
- "REPORTS" page heading at top-left of content area.
- **Filter bar** directly below: Date range, Quiz selector, Game Mode selector, Section filter.
- **Export button** top-right: "📥 Export CSV".

### Report Sub-Navigation (Tabs)

```
[ Game Sessions ]  [ Section Scoreboard ]  [ Student Performance ]  [ Question Analysis ]  [ Grading Sheets ]
```

---

## Tab 1 — Game Session History

A paginated table of all past game sessions.

| Column | Description |
|--------|-------------|
| # | Session number |
| Date & Time | When the game was played |
| Quiz Name | Which quiz was used |
| Game Mode | Classic / Accuracy / Confidence / Pyramid / Color Kingdom / Duel |
| Sections Played | Which sections/databases participated |
| Total Players | Number of students who joined |
| Duration | Total time from start to finish |
| Winner / Top Score | First place student or team |

Click any row → **Session Detail View**:
- Per-question breakdown: % correct, average time, which choice was most selected.
- Full player/team leaderboard for that session.
- Per-student answer log (exportable).

---

## Tab 2 — Section Scoreboard ⭐ (KEY FEATURE)

**Purpose**: Tally and compare scores by section across all game sessions.

### UI Layout

**Section summary cards** (one per section that has played):

```
┌─────────────────────────────────────────┐
│  10 – STEM 1                            │
│  📊 Total Games Played: 12              │
│  ✅ Total Correct Answers: 842          │
│  📈 Average Score: 74.3%               │
│  🏆 Top Student: Juan Reyes (2024-56789)│
│  👥 Students: 28                        │
└─────────────────────────────────────────┘
```

Below the cards: a **comparison bar chart** showing all sections side-by-side with:
- Average score per section (bar height).
- Color-coded per section.
- Hover tooltip: section name + exact stats.

### Section Leaderboard Table (below bar chart)

| Rank | Section | Total Games | Avg Score | Correct % | Top Student |
|------|---------|------------|-----------|-----------|-------------|
| 1 | 10 – STEM 1 | 12 | 82% | 79% | Juan Reyes |
| 2 | 10 – Arts 2 | 10 | 74% | 71% | Maria Santos |
| 3 | 9 – Science A | 8 | 68% | 65% | Alex Johnson |

**Filter controls**:
- Filter by Quiz (show only scores from a specific quiz).
- Filter by Game Mode.
- Filter by Date Range.
- Filter by Student Database Group.

### Per-Section Drill-Down

Click a section row → opens a detailed view:
- All students in the section ranked by total score.
- Per-student: games played, correct answers, wrong answers, accuracy %, average score.
- Question-level heatmap: which questions that section answered correctly most/least.

---

## Tab 3 — Student Performance

Search by name or ID. Shows per-student detail:

| Field | Value |
|-------|-------|
| ID Number | 2024-56789 |
| Thai Name | นาย ฮวน เรเยส |
| English Name | Juan Miguel D. Reyes |
| Section | STEM 1 |
| Class Number | 16 |
| Total Games Played | 8 |
| Total Score | 6,450 pts |
| Average Score | 806 pts/game |
| Overall Accuracy | 84% |
| Best Game Mode | Classic Mode |
| Current Streak (if applicable) | 3 |

**Score trend line chart** (x-axis = game sessions chronologically, y-axis = score).

---

## Tab 4 — Question Analysis

Per-question performance across all games where that question was used:

| Column | Description |
|--------|-------------|
| Question Text | Truncated with "see more" link |
| Quiz | Which quiz it belongs to |
| Times Asked | How many sessions included this question |
| % Correct | Overall correct response rate |
| Most Chosen Wrong Answer | The most common incorrect choice |
| Average Time Taken | Mean response time in seconds |
| Difficulty Rating | Auto-calculated: Easy / Medium / Hard based on % correct |

Sort by difficulty (hardest first) to identify questions that need review.

---

## Tab 5 — Grading Sheets ⭐ (KEY FEATURE)

> Full specification is in [`GRADING_SHEET_PROMPT.md`](./GRADING_SHEET_PROMPT.md).

Two sub-tabs:

### Sub-Tab A — Section Grading Sheet
- Select a **Database** + **Section** → generates a grade table for that section only.
- Columns: Class No, ID Number, English Name, Thai Name, one column per game played, Total Score, Max Possible, **Total Game Score %**.
- **Highest Possible Score per game** = the winner's score in that game session.
- **Total Game Score %** = student's total score ÷ their total max possible × 100.
- Students absent from a game get `—`; that game's max is excluded from their personal denominator.
- Bottom row = section average.
- **`📥 Download Section Grading Sheet (.xlsx)`** button.

### Sub-Tab B — Whole DB Grading Sheet
- Select a **Database** → shows all students across all sections in one table.
- Columns: Rank, Section, Class No, ID Number, English Name, Thai Name, Total Score, Max Possible, Total Game Score %, **Final Grade**.
- **Uniform Total** input (default 100): teacher sets the maximum marks this category is worth. `Final Grade = Total Game Score % × Uniform Total`. Updates live as teacher types.
- Toggle: **Group by Section** (adds section subheader rows + section average rows).
- **`📥 Download Whole DB Grading Sheet (.xlsx)`** button — Excel file includes a sheet per section plus one combined sheet.

---

## Export Options

| Export | Format | Content |
|--------|--------|---------|
| Export Section Scoreboard | CSV / Excel | All sections, all stats |
| Export Student Scores | CSV | All students, all game scores |
| Export Session Detail | CSV | Per-session full player log |
| Export Question Analysis | CSV | Per-question stats |
| **Section Grading Sheet** | **Excel (.xlsx)** | **One section: per-student game scores + Total Game Score %** |
| **Whole DB Grading Sheet** | **Excel (.xlsx)** | **All students in DB: Total Game Score % + Final Grade (Uniform Total)** |

All exports respect the currently active filters.

---

## Frontend Architecture

```
ReportsPage
  ├── PageHeader ("REPORTS" + ExportButton)
  ├── FilterBar
  │     ├── DateRangePicker
  │     ├── QuizSelector (dropdown)
  │     ├── GameModeFilter (multi-select)
  │     └── SectionFilter (dropdown, lists all sections in DB)
  ├── ReportTabs
  │     ├── GameSessionHistoryTab
  │     │     ├── SessionTable (paginated, clickable)
  │     │     └── SessionDetailPanel (slide-in or modal)
  │     ├── SectionScoreboardTab ⭐
  │     │     ├── SectionSummaryCards[]
  │     │     ├── SectionComparisonBarChart
  │     │     ├── SectionLeaderboardTable (sortable)
  │     │     └── SectionDrillDownView (per-section student table)
  │     ├── StudentPerformanceTab
  │     │     ├── StudentSearchBar
  │     │     ├── StudentDetailCard
  │     │     │     ├── StudentInfoBlock (all 5 DB fields)
  │     │     │     ├── StatsSummary
  │     │     │     └── ScoreTrendLineChart
  │     │     └── StudentFullHistoryTable
  │     ├── QuestionAnalysisTab
  │     │     └── QuestionStatsTable (sortable by difficulty)
  │     └── GradingSheetsTab ⭐
  │           ├── GradingSheetSubNav
  │           │     ├── SectionGradingSheetPanel
  │           │     │     ├── FilterBar (DB, Section, Games, Date)
  │           │     │     ├── GradingTable (frozen cols, game cols, % col)
  │           │     │     └── DownloadXlsxButton
  │           │     └── WholeDBGradingSheetPanel
  │           │           ├── UniformTotalInput (live Final Grade preview)
  │           │           ├── FilterBar + GroupBySectionToggle
  │           │           ├── GradingTable (all students, Final Grade col)
  │           │           └── DownloadXlsxButton
```

---

## Backend Architecture

### API Endpoints
```
GET /api/reports/sessions
Query: ?quizId=&mode=&from=&to=&section=&databaseId=
Response: { sessions: SessionSummary[], total: number }

GET /api/reports/sessions/:sessionId/detail
Response: { questions: QuestionResult[], players: PlayerScore[] }

GET /api/reports/sections
Query: ?quizId=&mode=&from=&to=&databaseId=
Response: {
  sections: [{
    section: string,
    studentCount: number,
    gamesPlayed: number,
    totalCorrect: number,
    avgScore: number,
    accuracyPercent: number,
    topStudent: { idNumber, englishName, score }
  }]
}

GET /api/reports/sections/:sectionName/students
Query: ?databaseId=
Response: {
  students: [{
    idNumber, thaiName, englishName, section, classNumber,
    gamesPlayed, totalScore, avgScore, accuracy
  }]
}

GET /api/reports/students/:studentId
Response: {
  info: Student,
  stats: { gamesPlayed, totalScore, avgScore, accuracy, bestMode },
  scoreHistory: [{ date, sessionId, quizName, score }]
}

GET /api/reports/questions
Query: ?quizId=&mode=&from=&to=
Response: { questions: QuestionStat[] }

GET /api/reports/export
Query: ?type=sections|students|sessions|questions&format=csv&[filters]
Response: text/csv  (file download)

GET /api/reports/grading/section
Query: ?databaseId=&section=&gameIds=&from=&to=
Response: { section, databaseName, games[], students[] }  — see GRADING_SHEET_PROMPT.md

GET /api/reports/grading/database
Query: ?databaseId=&gameIds=&from=&to=
Response: { databaseName, totalStudents, games[], students[] }

GET /api/reports/grading/export
Query: ?type=section|database&databaseId=&section=&uniformTotal=&format=xlsx
Response: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
```

### Database Queries — Section Tally

The section scoreboard aggregates directly from `game_answers` joined to `students`:

```sql
SELECT
  s.section,
  s.database_id,
  COUNT(DISTINCT gp.id)                AS student_count,
  COUNT(DISTINCT gs.id)                AS games_played,
  SUM(CASE WHEN ga.is_correct THEN 1 ELSE 0 END) AS total_correct,
  COUNT(ga.id)                         AS total_answers,
  ROUND(AVG(gp.score), 2)             AS avg_score,
  ROUND(
    SUM(CASE WHEN ga.is_correct THEN 1 ELSE 0 END)::numeric
    / NULLIF(COUNT(ga.id), 0) * 100, 2
  )                                    AS accuracy_percent
FROM game_answers ga
JOIN game_players gp  ON gp.id = ga.player_id
JOIN students s       ON s.id  = gp.student_id
JOIN game_sessions gs ON gs.id = ga.game_id
WHERE gs.status = 'finished'
  -- optional filters applied here
GROUP BY s.section, s.database_id
ORDER BY avg_score DESC;
```
