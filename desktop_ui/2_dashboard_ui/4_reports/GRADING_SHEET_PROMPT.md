# EduVerse — Grading Sheet (Desktop / Teacher View) UI Prompt

---

## Overview

The **Grading Sheet** is a tab inside Reports. It converts raw game scores into a structured grade sheet that teachers can review, adjust, and export to Excel. There are two levels:

1. **Section Grading Sheet** — one sheet per section showing every student's game-by-game scores and their computed Total Game Score %.
2. **Whole DB Grading Sheet** — one sheet covering all students across all sections in a selected database, with a teacher-configurable **Uniform Total** that scales each student's Total Game Score % into a final grade.

Both are downloadable as **`.xlsx` Excel files**.

---

## Core Scoring Concept

### Highest Possible Score (per game)
> The score of the **student who won (ranked 1st) in that game session**.
> This becomes the ceiling — the "perfect score" — for that game.

### Total Game Score % (per student)
```
Total Game Score % =
  Σ (student's score in each game they participated in)
  ─────────────────────────────────────────────────────  × 100
  Σ (highest possible score for each of those games)
```

**Example:**

| Game | Student Score | Highest Possible (Winner's Score) |
|------|--------------|----------------------------------|
| Game 1 — Classic Quiz | 820 | 1000 |
| Game 2 — Accuracy Quiz | 640 | 800 |
| Game 3 — Pyramid | 500 | 700 |
| **Total** | **1960** | **2500** |

**Total Game Score % = 1960 ÷ 2500 × 100 = 78.40%**

### Uniform Total (Whole DB sheet only)
The teacher enters a number (e.g. `50`, `100`, `30`) that represents the **maximum marks this grading category is worth** in their school's grade book.

```
Final Grade = Total Game Score %  ×  (Uniform Total ÷ 100)
```

Example with Uniform Total = 50:
```
78.40%  ×  (50 ÷ 100)  =  39.20 / 50
```

---

## Tab 5 — Grading Sheets (inside Reports)

### Tab Sub-Navigation

```
[ Section Grading Sheet ]   [ Whole DB Grading Sheet ]
```

At the top of both sub-tabs:

```
┌──────────────────────────────────────────────────────┐
│  Database:  [ Grade 10 ▼ ]   Section: [ STEM 1 ▼ ]  │
│  Include Games:  [ All Games ▼ ]  Date: [ All ▼ ]   │
│                              [ 📥 Download Excel ]   │
└──────────────────────────────────────────────────────┘
```

---

## Sub-Tab A — Section Grading Sheet

### Purpose
One grading sheet per section. Lists every student in the section as a row, every game played as a column, and computes Total Game Score %.

---

### Screen Layout

**Header row (above table):**
```
Section:  STEM 1            Database:  Grade 10
Students: 28                Games Included: 5
```

**Grading table:**

| # | Class No | ID Number | English Name | Thai Name | Game 1 | Game 2 | Game 3 | Game 4 | Game 5 | Total Score | Max Possible | Total Game Score % |
|---|----------|-----------|--------------|-----------|--------|--------|--------|--------|--------|-------------|--------------|-------------------|
| 1 | 16 | 2024-56789 | Juan Reyes | นาย ฮวน | 820 | 640 | 500 | 780 | 910 | 3,650 | 4,500 | **81.11%** |
| 2 | 17 | 2024-56790 | Maria Santos | นางสาว มาเรีย | 750 | 800 | 420 | 650 | 870 | 3,490 | 4,500 | **77.56%** |
| 3 | 18 | 2024-56791 | Alex Johnson | นาย อเล็กซ์ | — | 700 | 510 | 720 | 880 | 2,810 | 3,600 | **78.06%** |
| … | … | … | … | … | … | … | … | … | … | … | … | … |
| — | — | — | **Section Avg** | — | 790 | 688 | 476 | 710 | 890 | 3,554 | 4,500 | **78.98%** |

> **"—" (dash)** = student did not participate in that game. That game's Highest Possible Score is excluded from that student's Max Possible total.

**Column headers for game columns:**
Each game column header shows:
- Game number (e.g. "Game 3")
- Date (e.g. "May 15")
- Quiz name (e.g. "Math Quiz")
- Mode icon (🏆 / 🎯 / 🧠 / 🏛 / 👑 / ⚔)
- Highest Possible Score (e.g. "Max: 1000")

Hovering the column header shows a tooltip:
```
Game 3 — Pyramid
May 15, 2026
Quiz: Math Unit 3
Mode: The Lost Pyramid
Highest Possible Score: 700 (winner: Alex — Team Gold)
Players from this section: 26 / 28
```

**Section Summary footer row:**
- Total Score = sum of all students' scores (all games).
- Max Possible = sum of all highest possible scores across all games × number of students.
- Section Total Game Score % = sum of all student scores ÷ sum of all students' max possible × 100.

---

### Rules

1. A student with `—` (absent from that game) has that game's winner score **excluded** from their personal Max Possible total. Their % is calculated only from games they participated in.
2. Games are shown in **chronological order** left to right.
3. Columns are frozen at the student info columns (Class No, ID, English Name, Thai Name) so the teacher can scroll right through many games.
4. The table is read-only — scores are not editable here.
5. **Rank column** (optional, togglable): shows each student's rank within the section by Total Game Score %.

---

### Download Section Grading Sheet (Excel)

Button: **`📥 Download Section Grading Sheet (.xlsx)`**

**Excel file structure:**

- **Sheet 1: "STEM 1 — Grading Sheet"**

Row 1 (title): `EduVerse Grading Sheet — Section: STEM 1 — Database: Grade 10`
Row 2 (generated): `Generated: July 3, 2026   Games Included: 5   Date Range: All`
Row 3: blank
Row 4 (column headers): `Rank | Class No | ID Number | English Name | Thai Name | [Game 1 header] | [Game 2 header] | … | Total Score | Max Possible | Total Game Score %`
Row 5+: one row per student, sorted by Total Game Score % descending.
Last row: Section Average / Total row, bold.

Cell formatting:
- Total Game Score % column: bold, formatted as `0.00%`.
- Absent (`—`) cells: grey background, dashes.
- Top student row: gold background.
- Bottom student row (lowest %): light-red background.
- Column widths auto-fitted.
- Header rows frozen (rows 1–4).

- **Sheet 2: "Game Details"**
  One row per game with: Game #, Date, Quiz Name, Mode, Winner Name, Winner Score (Highest Possible Score).

---

## Sub-Tab B — Whole DB Grading Sheet

### Purpose
One grading sheet covering **all students in the selected database** (all sections combined). Teacher sets a **Uniform Total** to scale each student's Total Game Score % into a final points value.

---

### Uniform Total Input

At the top of the sub-tab, prominently:

```
┌───────────────────────────────────────────────────────────────────┐
│  UNIFORM TOTAL                                                    │
│  This grade category is worth  [ 50 ]  points in the grade book. │
│  Final Grade = Total Game Score %  ×  Uniform Total              │
│                                                                   │
│  Example: 78.40%  ×  50  =  39.20 / 50                          │
└───────────────────────────────────────────────────────────────────┘
```

- Input field: number, default `100`, range 1–1000, accepts decimals.
- Updates the **Final Grade** column live as teacher types (no save needed).
- **The Uniform Total is not saved to the database** — it is a per-export setting only.

---

### Screen Layout

**Header row (above table):**
```
Database:   Grade 10          Total Students: 84
Sections:   STEM 1, Arts 2, Science A     Games Included: 5
Uniform Total:  50 pts
```

**Grading table:**

| # | Section | Class No | ID Number | English Name | Thai Name | Total Score | Max Possible | Total Game Score % | Final Grade (/ 50) |
|---|---------|----------|-----------|--------------|-----------|-------------|--------------|-------------------|--------------------|
| 1 | STEM 1 | 16 | 2024-56789 | Juan Reyes | นาย ฮวน | 3,650 | 4,500 | 81.11% | **40.56** |
| 2 | STEM 1 | 17 | 2024-56790 | Maria Santos | นางสาว มาเรีย | 3,490 | 4,500 | 77.56% | **38.78** |
| 3 | Arts 2 | 05 | 2024-56850 | Kevin Tan | นาย เควิน | 3,100 | 4,100 | 75.61% | **37.80** |
| … | … | … | … | … | … | … | … | … | … |

**Sorted** by Total Game Score % descending by default (overall ranking). Teacher can re-sort by any column.

**Section separator rows** (optional toggle): when "Group by Section" is ON, a grey subheader row appears between sections showing the section name and section average.

**Summary footer:**
```
DATABASE TOTAL   |   84 students   |   Avg: 74.23%   |   Avg Final Grade: 37.12 / 50
```

---

### Grouping and Filter Controls

```
[ Group by Section: ON/OFF ]   [ Sort by: Total Game Score % ▼ ]
[ Show: All Sections ▼ ]       [ Games: All ▼ ]
```

- **Group by Section ON**: rows are sorted within their section, with section subheaders. Section average row shown at the end of each group.
- **Group by Section OFF**: all students sorted globally by Total Game Score % (cross-section ranking).

---

### Download Whole DB Grading Sheet (Excel)

Button: **`📥 Download Whole DB Grading Sheet (.xlsx)`**

**Excel file structure:**

- **Sheet 1: "Grade 10 — All Students"**

Row 1: `EduVerse Grading Sheet — Database: Grade 10 — Uniform Total: 50 pts`
Row 2: `Generated: July 3, 2026   Games: 5   Total Students: 84`
Row 3: blank
Row 4 (headers): `Rank | Section | Class No | ID Number | English Name | Thai Name | Total Score | Max Possible | Total Game Score % | Final Grade (/ 50)`
Row 5+: one row per student, sorted by Total Game Score % descending.
Last row: Database Average row, bold.

Cell formatting (same as Section sheet):
- Final Grade column: bold, formatted as `0.00`.
- Total Game Score %: formatted as `0.00%`.
- Top 3 students: gold / silver / bronze row background.
- Absent games shown as `0` in game columns (not `—`, for Excel SUM compatibility).
- Column widths auto-fitted.
- Rows 1–4 frozen.

- **Sheet 2: "By Section"**
  One sheet tab per section (or one table per section separated by blank rows), showing the same data filtered to that section + that section's own average row.

- **Sheet 3: "Game Details"**
  Same as Section sheet's Sheet 2 — lists all games with their winner and Highest Possible Score.

---

## Backend — Grading Sheet API

### Endpoint: Section Grading Sheet
```
GET /api/reports/grading/section
Query params:
  databaseId    (required)
  section       (required, e.g. "STEM 1")
  gameIds?      (comma-separated, filter to specific sessions; default = all)
  from?         (ISO date)
  to?           (ISO date)

Response:
{
  section: "STEM 1",
  databaseName: "Grade 10",
  games: [
    {
      gameId: "uuid",
      gameNumber: 1,
      date: "2026-05-10",
      quizName: "Math Quiz",
      mode: "classic",
      highestPossibleScore: 1000,   ← MAX(gp.score) for this session
      winnerName: "Juan Reyes"
    }
  ],
  students: [
    {
      classNumber: 16,
      idNumber: "2024-56789",
      englishName: "Juan Miguel D. Reyes",
      thaiName: "นาย ฮวน เรเยส",
      scores: {
        "gameId-1": 820,
        "gameId-2": 640,
        "gameId-3": null,   ← null = did not participate
        "gameId-4": 780,
        "gameId-5": 910
      },
      totalScore: 3650,
      maxPossible: 4500,         ← sum of highestPossibleScore for games they joined
      totalGameScorePercent: 81.11
    }
  ],
  sectionAvg: {
    totalScore: 3554,
    maxPossible: 4500,
    totalGameScorePercent: 78.98
  }
}
```

### Endpoint: Whole DB Grading Sheet
```
GET /api/reports/grading/database
Query params:
  databaseId    (required)
  gameIds?
  from?
  to?

Response:
{
  databaseName: "Grade 10",
  totalStudents: 84,
  games: [ ...same as above... ],
  students: [
    {
      section: "STEM 1",
      classNumber: 16,
      idNumber: "2024-56789",
      englishName: "Juan Miguel D. Reyes",
      thaiName: "นาย ฮวน เรเยส",
      totalScore: 3650,
      maxPossible: 4500,
      totalGameScorePercent: 81.11
      // Note: uniformTotal is NOT stored here — it is applied client-side
    }
  ],
  databaseAvg: {
    totalGameScorePercent: 74.23
  }
}
```
> **Uniform Total is applied client-side only.** The server returns raw `totalGameScorePercent`. The browser multiplies by `(uniformTotal / 100)` when rendering and when generating the Excel file.

### Endpoint: Excel Download
```
GET /api/reports/grading/export
Query params:
  type          "section" | "database"
  databaseId    (required)
  section?      (required if type = "section")
  gameIds?
  from?
  to?
  uniformTotal? (number, used only if type = "database"; default 100)
  format        "xlsx"  (only Excel supported for grading sheets)

Response: application/vnd.openxmlformats-officedocument.spreadsheetml.sheet
Content-Disposition: attachment; filename="EduVerse_GradingSheet_STEM1.xlsx"
```

### SQL — Highest Possible Score Per Game
```sql
-- Highest Possible Score for a game session = winner's score
SELECT
  gs.id          AS game_id,
  MAX(gp.score)  AS highest_possible_score
FROM game_sessions gs
JOIN game_players gp ON gp.game_id = gs.id
WHERE gs.status = 'finished'
GROUP BY gs.id;
```

### SQL — Student Total Game Score % (for one section)
```sql
-- Step 1: get each student's score per game (NULL if absent)
SELECT
  s.id              AS student_id,
  s.class_number,
  s.id_number,
  s.english_name,
  s.thai_name,
  gs.id             AS game_id,
  gp.score          AS student_score,
  winners.highest   AS highest_possible_score
FROM students s
CROSS JOIN game_sessions gs
LEFT JOIN game_players gp
  ON gp.student_id = s.id AND gp.game_id = gs.id
JOIN (
  SELECT game_id, MAX(score) AS highest FROM game_players GROUP BY game_id
) winners ON winners.game_id = gs.id
WHERE s.database_id = :databaseId
  AND s.section     = :section
  AND gs.status     = 'finished'
  AND (:from IS NULL OR gs.started_at >= :from)
  AND (:to   IS NULL OR gs.started_at <= :to);

-- Step 2: aggregate per student
SELECT
  student_id,
  SUM(COALESCE(student_score, 0))          AS total_score,
  SUM(CASE WHEN student_score IS NOT NULL
           THEN highest_possible_score
           ELSE 0 END)                      AS max_possible,
  ROUND(
    CAST(SUM(COALESCE(student_score, 0)) AS REAL)
    / NULLIF(SUM(CASE WHEN student_score IS NOT NULL
                      THEN highest_possible_score ELSE 0 END), 0)
    * 100, 2
  )                                         AS total_game_score_percent
FROM [step_1_result]
GROUP BY student_id;
```

---

## Excel Generation Library

Use **`exceljs`** (Node.js) to generate `.xlsx` files server-side.

```
npm install exceljs
```

Key features used:
- `worksheet.addRow()` for data rows.
- `cell.fill` for gold/silver/bronze/absent cell background colors.
- `cell.font` for bold headers and Total Game Score % column.
- `cell.numFmt = '0.00%'` for percentage cells.
- `worksheet.views = [{ state: 'frozen', ySplit: 4 }]` to freeze header rows.
- `worksheet.columns = [{ width: 20 }, ...]` for column widths.
- Multiple worksheets via `workbook.addWorksheet('Sheet Name')`.
```

---

## UI Placement in Reports

```
ReportsPage
  └── ReportTabs
        ├── [ Game Sessions ]
        ├── [ Section Scoreboard ]
        ├── [ Student Performance ]
        ├── [ Question Analysis ]
        └── [ Grading Sheets ]  ← NEW TAB
              ├── GradingSheetSubNav
              │     ├── [ Section Grading Sheet ]
              │     └── [ Whole DB Grading Sheet ]
              │
              ├── — Section Grading Sheet —
              │     ├── FilterBar (Database, Section, Games, Date)
              │     ├── GradingTable (frozen columns, game columns)
              │     │     ├── StudentRows[]
              │     │     └── SectionAverageFooterRow
              │     └── DownloadButton ("📥 Download Section Grading Sheet (.xlsx)")
              │
              └── — Whole DB Grading Sheet —
                    ├── UniformTotalInput (live preview of Final Grade column)
                    ├── FilterBar (Database, Games, Date, Group by Section toggle)
                    ├── GradingTable (all students, all sections)
                    │     ├── [SectionSubheader — when grouped]
                    │     ├── StudentRows[]
                    │     ├── [SectionAverageRow — when grouped]
                    │     └── DatabaseAverageFooterRow
                    └── DownloadButton ("📥 Download Whole DB Grading Sheet (.xlsx)")
```
