# EduVerse — Sound & Media Architecture

---

## Overview

EduVerse supports **fully customizable audio** for every game mode and every event within each game. Teachers upload their own sound files via the **Settings → Sound & Music** panel. Factory default sounds are included for all slots.

---

## Audio Storage Structure (Local Filesystem)

```
storage/
└── sounds/
    ├── defaults/                          ← Factory default sounds (bundled with app)
    │     ├── classic/
    │     │     ├── lobby_music.mp3
    │     │     ├── player_join.mp3
    │     │     ├── game_start.mp3
    │     │     ├── question_reveal.mp3
    │     │     ├── timer_tick.mp3
    │     │     ├── time_up.mp3
    │     │     ├── correct_reveal.mp3
    │     │     ├── wrong_reveal.mp3
    │     │     ├── leaderboard_music.mp3
    │     │     └── winner_sound.mp3
    │     ├── accuracy/
    │     │     └── (same pattern)
    │     ├── confidence/
    │     │     ├── conf_high_correct.mp3
    │     │     ├── conf_high_wrong.mp3
    │     │     └── (other slots)
    │     ├── pyramid/
    │     │     ├── lobby_music.mp3
    │     │     ├── game_start_fanfare.mp3
    │     │     ├── team_advances.mp3
    │     │     ├── team_falls.mp3
    │     │     └── (other slots)
    │     ├── colorkingdom/
    │     │     ├── lobby_music.mp3
    │     │     ├── hex_capture.mp3
    │     │     └── (other slots)
    │     └── duel/
    │           ├── lobby_tension.mp3
    │           ├── duel_start.mp3
    │           └── (other slots)
    │
    └── custom/                            ← Teacher-uploaded custom sounds
          ├── classic/
          │     ├── lobby_music.mp3         ← overrides default if exists
          │     └── (only slots that were uploaded)
          ├── accuracy/
          ├── confidence/
          ├── pyramid/
          ├── colorkingdom/
          └── duel/
```

---

## Audio File Specifications

| Property | Requirement |
|----------|-------------|
| Accepted formats | `.mp3`, `.wav`, `.ogg`, `.m4a` |
| Maximum file size | 10 MB per file |
| Recommended bit rate | 128 kbps (mp3) |
| Recommended sample rate | 44100 Hz |
| Recommended duration | Lobby music: loop-ready; event sounds: 0.5–5 seconds |

---

## Sound Slot Resolution Logic

```
function getSoundPath(gameMode, slotKey, userId):
  customPath = "storage/sounds/custom/{gameMode}/{slotKey}.*"
  if file exists at customPath:
    return customPath              ← use teacher's custom upload
  else:
    return "storage/sounds/defaults/{gameMode}/{slotKey}.mp3"  ← use factory default
```

---

## Backend: Sound File API

```
// Upload custom sound
POST /api/settings/sound/:gameMode/:slotKey
Content-Type: multipart/form-data
Body: { file: <audio file> }
→ Saves to storage/sounds/custom/:gameMode/:slotKey.:ext
→ Updates sound_settings table
Response: { slotKey, fileUrl: "/sounds/custom/:gameMode/:slotKey.mp3", uploadedAt }

// Preview a sound slot
GET /api/settings/sound/:gameMode/:slotKey/preview
→ Streams the resolved audio file (custom if exists, else default)
→ Content-Type: audio/mpeg

// Remove custom sound (revert to default)
DELETE /api/settings/sound/:gameMode/:slotKey
→ Deletes file from storage/sounds/custom/
→ Removes record from sound_settings
Response: { slotKey, revertedToDefault: true }

// List all sound settings for a user
GET /api/settings/sound
Response: {
  masterVolume: 80,
  isMuted: false,
  slots: {
    "classic": {
      "lobby_music":     { hasCustom: true,  fileName: "my_music.mp3",  sizekB: 4200 },
      "player_join":     { hasCustom: false, fileName: "default",        sizekB: null },
      ...
    },
    "pyramid": { ... },
    ...
  }
}
```

---

## Frontend: Sound Playback Engine

### Sound Manager (Singleton)
```js
class SoundManager {
  private audioContext: AudioContext
  private masterVolume: number  // 0.0–1.0
  private isMuted: boolean
  private loopingTrack: HTMLAudioElement | null

  // Play a one-shot sound event
  play(gameMode: string, slotKey: string): void

  // Start a looping background track (stops any previous loop)
  playLoop(gameMode: string, slotKey: string): void

  // Stop the current loop
  stopLoop(): void

  // Set master volume (0–100)
  setVolume(percent: number): void

  // Toggle global mute
  setMuted(muted: boolean): void
}
```

### How sounds are triggered in game flow
```
Game Event                  → SoundManager.play(gameMode, slotKey)
─────────────────────────────────────────────────────────────
Lobby screen opens          → playLoop('classic', 'lobby_music')
Player joins                → play('classic', 'player_join')
Game starts                 → stopLoop() + play('classic', 'game_start')
Question revealed           → play('classic', 'question_reveal')
Timer hits 5 seconds        → play('classic', 'timer_tick')  [loop 5x]
Timer hits 0                → play('classic', 'time_up')
Correct answer revealed     → play('classic', 'correct_reveal')
Wrong answer revealed       → play('classic', 'wrong_reveal')
Leaderboard shown           → playLoop('classic', 'leaderboard_music')
Winner announced            → stopLoop() + play('classic', 'winner_sound')
```

---

## Student Device Audio

**Student devices have NO audio, music, or video — none whatsoever.**

Student devices display only:
- The question text.
- The answer choices (A / B / C / D buttons).
- Their current score / result feedback (text/color only).

All sound and music plays exclusively on the **teacher's desktop screen**. This applies to every game mode including Duel Mode and Color Kingdom. There are no student-side sound slots, no student audio upload settings, and no audio files served to student devices.

---

## Image & Media Storage

Beyond audio, EduVerse also stores images locally:

```
storage/
└── images/
    ├── questions/          ← Images attached to question text
    │     └── {questionId}.jpg
    ├── choices/            ← Images attached to answer choices
    │     └── {choiceId}.jpg
    └── avatars/            ← Teacher profile pictures
          └── {userId}.jpg
```

### Image Upload API
```
POST /api/upload/question-image
POST /api/upload/choice-image
POST /api/upload/avatar
→ All saved locally, served via /storage/images/...
→ Accepted: .jpg, .jpeg, .png, .webp, max 5MB
```
