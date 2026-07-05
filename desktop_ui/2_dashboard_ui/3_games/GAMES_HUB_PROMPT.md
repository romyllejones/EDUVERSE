# EduVerse — Games Hub (Desktop / Teacher View) UI Prompt

---

## Overview

The **Games** section is accessed from the sidebar. It is NOT a standalone page — instead, game modes are launched directly from the **Available Quizzes** card menu. The sidebar "GAMES" item expands to show a submenu of all available modes.

---

## How a Teacher Launches a Game

```
Sidebar → AVAILABLE QUIZES
        ↓
Quiz Card → Click hamburger menu (≡)
        ↓
Dropdown options:
  ▶ Play Game    → opens "Select Game Mode" modal
  👥 Assign Game  → assigns quiz to a class (scheduled)
  📖 Review Mode  → teacher reviews answers solo
  ⚔ Duel Mode    → launches 1-vs-1 setup
  ✏ Edit Quiz    → returns to Create Questions editor
```

---

## "Select Game Mode" Modal

When teacher clicks **Play Game**, a modal appears with game mode cards:

### Modal Layout
- Title: "SELECT GAME MODE" in bold white.
- 3×2 grid of mode cards (or scrollable list on smaller screens).
- Each card:
  - Game mode icon (matching the lobby screen icon).
  - Game mode name.
  - Short one-line description.
  - Color accent matching the mode's theme.
  - "SELECT" button.

### Mode Cards

| Card | Icon | Description | Color Accent |
|------|------|-------------|-------------|
| Classic Mode | 🏆 Trophy | "Speed + accuracy. Individual competition." | Blue/Cyan |
| Accuracy Mode | 🎯 Target | "Accuracy only. Take your time." | Teal |
| Confidence Mode | 🧠 Brain | "Rate your confidence. High risk, high reward." | Purple |
| The Lost Pyramid | 🏛 Pyramid | "Team race up the pyramid. 2–4 teams." | Gold/Amber |
| Color Kingdom | 👑 Crown | "Conquer the hex map. Team territory battle." | Multi-color |
| Duel Mode | ⚔ Sword | "1-vs-1 head-to-head challenge." | Red/Blue split |

### Post-Selection — Game Config Panel

After selecting a mode, a brief configuration panel appears:

**All Modes:**
- Select Student Database (dropdown of all uploaded databases).
- Question time limit (seconds, or "No Limit").

**Team Modes Only (Pyramid, Color Kingdom):**
- Number of teams: 2 / 3 / 4 (toggle).
- Steps to win (Pyramid only): 10 / 15 / 20.
- Hex grid size (Color Kingdom only): Small / Medium / Large.

**Confidence Mode:**
- Enable score penalty for wrong answers: toggle ON/OFF.

**Duel Mode:**
- Win threshold (rounds needed to win): 3 / 5 / 7.

**"LAUNCH GAME" button** → creates game session → shows lobby screen.

---

## Sidebar Games Submenu

When teacher clicks "GAMES" in sidebar:

```
GAMES  ▼
  ├── Classic Mode
  ├── Accuracy Mode
  ├── Confidence Mode
  ├── The Lost Pyramid
  ├── Color Kingdom
  └── Duel Mode
```

Each submenu item, when clicked, shows a "Quick Launch" panel:
- Select which quiz to use (dropdown).
- Select student database.
- Set basic config.
- Launch.

---

## Game Mode Comparison (Teacher Reference)

| Mode | Best For | Time Needed | Team? | Recommended Class Size |
|------|----------|------------|-------|------------------------|
| Classic | Fun review, engagement | Fast (15–30 min) | No | Any |
| Accuracy | Assessments, exams | Medium (20–40 min) | No | Any |
| Confidence | Metacognition lessons | Medium | No | Any |
| Lost Pyramid | Team collaboration | Long (30–60 min) | Yes (2–4) | 10–40 students |
| Color Kingdom | Team strategy | Long (30–60 min) | Yes (2–4) | 10–40 students |
| Duel | Spotlight competitions | Short (10–20 min) | No (2 players) | Any (audience watches) |
