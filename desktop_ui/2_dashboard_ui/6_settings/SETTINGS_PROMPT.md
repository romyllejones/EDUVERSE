# EduVerse — Settings (Desktop / Teacher View) UI Prompt

---

## Overview

The **Settings** page is accessible from the left sidebar. It allows teachers and administrators to configure all application preferences, including **sound and music assignment for every game mode**, account management, display preferences, and game defaults.

---

## Visual Design Prompt

### Layout
- Same dark navy dashboard layout: fixed left sidebar + top header + scrollable main content area.
- Settings content is organized into **tabs** or **accordion sections** across the main content panel.
- Each section has a clean heading, subtle divider line, and grouped form controls.
- Style must match the existing dashboard design: dark cards, cyan/blue accents, consistent border radius and spacing.

### Settings Sections (Tabs or Collapsible Cards)

1. **Account Settings**
2. **Sound & Music**
3. **Game Defaults**
4. **Display & Theme**
5. **Data & Storage**
6. **About / Version**

---

## Section 1 — Account Settings

### UI Elements
- **Profile Picture**: Circular avatar preview + "Change Photo" upload button (accepts `.jpg`, `.png`).
- **Display Name**: Text input (pre-filled with current name).
- **Username / Teacher ID**: Read-only text field (shown for reference).
- **Current Password**: Password input.
- **New Password**: Password input.
- **Confirm New Password**: Password input.
- **Save Changes** button (primary CTA — blue/cyan).
- **Administrator Panel** (visible to admin role only):
  - "Manage Teachers" button → opens teacher management sub-page.
  - "Add New Teacher" quick button.

---

## Section 2 — Sound & Music ⭐ (KEY FEATURE)

This is the most detailed settings section. It allows uploading custom audio files for every game mode and every sound event.

### Design
- Section header: "🎵 Sound & Music Settings" with a mute/unmute master toggle on the right.
- **Master Volume slider** (0–100%, with a speaker icon).
- **Global Mute toggle** — mutes all sounds when ON.
- Below: **per-game-mode accordion panels**, one for each game.

---

### Sound & Music — Per Game Mode

Each game mode has its own collapsible accordion panel with the following structure:

```
┌─────────────────────────────────────────────────────┐
│  [Game Icon]  CLASSIC MODE SOUNDS        [▼ expand] │
└─────────────────────────────────────────────────────┘
  When expanded:
  ┌───────────────────────────────────────────────────┐
  │ Sound Slot Name    [Current file / "Default"]  [▶ Preview] [📁 Upload] [✕ Remove] │
  └───────────────────────────────────────────────────┘
```

Each sound slot row contains:
- **Sound Slot Label** (e.g., "Lobby Background Music")
- **Description** (small gray text, e.g., "Plays while waiting for players to join")
- **Current File Display**: Shows filename if custom uploaded, or "Default" in muted text.
- **▶ Preview Button**: Plays the current sound (1–3 second preview).
- **📁 Upload Button**: Opens OS file picker filtered to audio files (`.mp3`, `.wav`, `.ogg`, `.m4a`).
- **✕ Remove Button**: Removes custom file and reverts to factory default.
- **File size limit**: Max 10 MB per file (shown as hint text).
- **Accepted formats**: `.mp3`, `.wav`, `.ogg`, `.m4a` (shown as hint text).

---

### Classic Mode — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Background Music | Loops while waiting for players to join |
| Player Join Sound | Plays each time a student joins the lobby |
| Game Start Sound | Plays when teacher clicks "Start Game" |
| Question Reveal Sound | Plays when each new question appears |
| Timer Countdown (Last 5s) | Tick-tock sound in final countdown |
| Time's Up Sound | Plays when the timer hits 0 |
| Correct Answer Reveal | Plays when correct answer is shown |
| Wrong Answer Reveal | Plays when wrong answers are shown |
| Leaderboard Music | Plays on the leaderboard screen |
| Winner Announcement Sound | Plays for the 1st place winner |

---

### Accuracy Mode — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Background Music | Ambient loop while waiting |
| Player Join Sound | Short chime on join |
| Game Start Sound | Start fanfare |
| Question Reveal Sound | Question appears |
| All Players Answered | Notification when everyone answers |
| Correct Answer Reveal | Shown after reveal |
| Wrong Answer Reveal | Shown after reveal |
| Streak Bonus Sound | Plays on 3-correct-streak milestone |
| Final Leaderboard Music | Celebration music |

---

### Confidence Mode — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Background Music | Mystical ambient loop |
| Player Join Sound | Soft chime |
| Game Start Sound | Dramatic intro |
| Question Reveal Sound | Question card animation |
| High Confidence + Correct | Level 3 correct answer sound |
| High Confidence + Wrong | Level 3 wrong answer sound |
| Medium Confidence + Correct | Level 2 correct |
| Medium Confidence + Wrong | Level 2 wrong |
| Low Confidence + Correct | Level 1 correct |
| Low Confidence + Wrong | Level 1 wrong |
| Answer Reveal Sound | Suspenseful reveal |
| Final Leaderboard Music | Victory fanfare |

---

### The Lost Pyramid — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Background Music | Egyptian ambient theme loop |
| Player Join Sound | Torch flicker / sand sound |
| Team Selection Music | Plays during team reveal screens |
| Game Start Fanfare | "Welcome to the Lost Pyramid" intro |
| Question Announce Sound | "QUESTION N" reveal |
| Question Active Music | Background music during question phase |
| Timer Countdown (Last 5s) | Sand-timer ticking |
| Time's Up Gong | Dramatic gong at 0 |
| Correct Answer Sound | Triumphant Egyptian horn |
| Wrong Answer Sound | Ominous low drone |
| Team Advances Sound | Avatar moves up pyramid steps |
| Team Falls Back Sound | Avatar slides back |
| One Team Reaches Top | First-to-reach-apex fanfare |
| Winner Proclamation Music | Epic final victory anthem |

---

### Color Kingdom — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Background Music | Royal fanfare loop |
| Player Join Sound | Coin/bell chime |
| Team Selection Music | Plays during "Choose Your Team" screen |
| Game Start Fanfare | "Welcome to Color Kingdom" intro |
| Question Reveal Sound | Question appears |
| Correct Answer Sound | Victory chime |
| Wrong Answer Sound | Low thud |
| Hex Capture Sound | Plays when hexes are conquered |
| Territory Battle Sound | Contested hex battle SFX |
| Win Condition Sound | Dominance milestone reached |
| Winner Proclamation Music | Royal victory anthem |

---

### Duel Mode — Sound Slots

| Slot Name | Description |
|-----------|-------------|
| Lobby Tension Music | Tense battle ambient loop |
| Player 1 Joins | Blue electric sound |
| Player 2 Joins | Red electric sound |
| Duel Start Sound | Battle horn clash |
| Question Reveal Sound | Quick whoosh |
| Player Buzzes In | Click/buzz answer sound |
| Player 1 Round Win | Cyan victory chime |
| Player 2 Round Win | Red victory chime |
| Both Wrong | Low drone |
| Final Winner Music | Championship fanfare |

---

### Background Music for Student Devices (per game mode)

In addition to the teacher/host sounds, the teacher can upload separate audio tracks that play on **student devices** (mobile/tablet) during each game phase:

| Game Mode | Student Device Sound Slots |
|-----------|---------------------------|
| All Modes | Student Lobby Waiting Music |
| All Modes | Student Answer Phase Music |
| All Modes | Student Correct Answer Feedback |
| All Modes | Student Wrong Answer Feedback |

---

## Section 3 — Game Defaults

| Setting | Type | Default |
|---------|------|---------|
| Default Question Timer (seconds) | Number input (5–120) | 30 |
| Minimum Players to Start Game | Number input (1–50) | 2 |
| Default Steps to Win (Pyramid) | Number input (5–30) | 15 |
| Default Hex Grid Size (Color Kingdom) | Dropdown (Small/Medium/Large) | Medium |
| Allow Rejoining After Disconnect | Toggle | ON |
| Show Leaderboard After Each Question | Toggle | ON |
| Enable Score Penalties (Confidence Mode) | Toggle | ON |
| Max Duel Win Threshold | Number input | 5 |
| Auto-advance Timer on Game Screens | Toggle | ON |
| Auto-advance Duration (seconds) | Number input | 5 |

---

## Section 4 — Display & Theme

| Setting | Type | Default |
|---------|------|---------|
| Application Theme | Toggle (Dark / Light) | Dark |
| Sidebar Behavior | Dropdown (Fixed / Collapsible / Auto-hide) | Fixed |
| Font Scale | Slider (80%–120%) | 100% |
| Enable Animations | Toggle | ON |
| Enable Particle Effects (Background) | Toggle | ON |
| Game Screen Resolution | Dropdown (1080p / 1440p / 4K) | 1080p |

---

## Section 5 — Data & Storage

| Action | Description |
|--------|-------------|
| Export All Quizzes | Downloads all quizzes as JSON file |
| Import Quizzes | Upload a `.json` quiz export file |
| Export Game Reports | Downloads all game history as CSV |
| Export Student Database | Downloads all student records as CSV |
| Clear Game History | Permanently deletes all game session records |
| Clear Student Database | Permanently deletes all student records |
| Reset to Factory Defaults | Resets all settings to default values |
| Clear All Custom Sounds | Removes all uploaded audio files, reverts to defaults |

All destructive actions require a **confirmation modal** before executing.

---

## Section 6 — About / Version

- Application name: **EduVerse**
- Version: displayed (e.g., `v3.5.2`)
- Build date.
- Developer info.
- License information.

---

## Frontend Architecture

```
SettingsPage
  ├── SettingsTabBar (or AccordionList)
  │     ├── AccountSettingsTab
  │     │     ├── AvatarUpload
  │     │     ├── DisplayNameInput
  │     │     ├── PasswordChangeForm
  │     │     └── AdminPanel (role-gated)
  │     ├── SoundMusicTab ⭐
  │     │     ├── MasterVolumeSlider
  │     │     ├── GlobalMuteToggle
  │     │     └── GameSoundAccordion[] (one per game mode)
  │     │           └── SoundSlotRow[]
  │     │                 ├── SlotLabel + Description
  │     │                 ├── CurrentFileDisplay
  │     │                 ├── PreviewButton
  │     │                 ├── UploadButton (file picker)
  │     │                 └── RemoveButton
  │     ├── GameDefaultsTab
  │     │     └── SettingControl[] (toggles, sliders, inputs)
  │     ├── DisplayThemeTab
  │     ├── DataStorageTab
  │     └── AboutTab
```

---

## Backend Architecture

### Settings API
```
GET    /api/settings
Response: { account: {...}, sound: {...}, gameDefaults: {...}, display: {...} }

PUT    /api/settings/account
Body: { displayName, currentPassword, newPassword, avatar? }

PUT    /api/settings/sound/:gameMode/:slotKey
Body: multipart/form-data { audioFile: File }
Response: { fileUrl: string, slotKey: string }

DELETE /api/settings/sound/:gameMode/:slotKey
Response: { slotKey: string, revertedToDefault: true }

GET    /api/settings/sound/:gameMode/:slotKey/preview
Response: audio/mpeg stream (for preview button)

PUT    /api/settings/gameDefaults
Body: { defaultTimer, minPlayers, ... }
```

### Audio File Storage
- All uploaded audio files stored in `storage/sounds/<gameMode>/<slotKey>.<ext>`.
- File metadata stored in the database (path, original name, size, uploaded_at).
- If a custom file exists for a slot, it is served instead of the factory default.
- Factory default sounds stored in `assets/sounds/defaults/<gameMode>/<slotKey>.mp3`.

### Database Table
```sql
sound_settings (
  id            UUID PRIMARY KEY,
  user_id       UUID REFERENCES users(id),
  game_mode     TEXT NOT NULL,     -- 'classic' | 'accuracy' | 'confidence' | 'pyramid' | 'colorkingdom' | 'duel'
  slot_key      TEXT NOT NULL,     -- e.g., 'lobby_music', 'player_join'
  file_path     TEXT,              -- NULL = use default
  original_name TEXT,
  file_size_kb  INTEGER,
  uploaded_at   TIMESTAMP DEFAULT NOW(),
  UNIQUE(user_id, game_mode, slot_key)
)

app_settings (
  id            UUID PRIMARY KEY,
  user_id       UUID REFERENCES users(id) UNIQUE,
  master_volume INTEGER DEFAULT 80,
  is_muted      BOOLEAN DEFAULT FALSE,
  game_defaults JSONB,
  display_prefs JSONB,
  updated_at    TIMESTAMP DEFAULT NOW()
)
```
