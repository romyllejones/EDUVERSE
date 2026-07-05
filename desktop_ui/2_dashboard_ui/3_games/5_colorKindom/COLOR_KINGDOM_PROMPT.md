# EduVerse — Color Kingdom (Desktop / Teacher Host View) UI Prompt

## Reference Images (in order)
1. `1_colorKingdomLobby.png`
2. `2_team_choose.png`
3. `3_team_reveal_a.png`
4. `4_welcome_to_color_kingdom.png`
5. `5_game_initial_state.png`
6. `6_game_fighting_state.png`
7. `7_winner_proclaimation.png`

---

## Visual Design Prompt

**Color Kingdom** is a territory-conquest team game. Teams expand colored hexagonal territory on a map by answering questions correctly.

---

### Screen 1 — Lobby: Waiting for Players

**Background**: Full-screen fantasy castle/royal hall interior. A grand arched doorway opens to a bright blue sky and fantasy castle towers beyond. Rich purple velvet banners hang from the ceiling. Gold lion statues flank either side.

**Title**: "**COLOR KINGDOM**" logo at top-center — stylized colorful text with a golden crown above the "O", gradient from blue to gold to green. Decorative royal crest/shield underneath.

**Center Text**: "**WAITING FOR PLAYERS**" in large, bold, golden text — dominant over the scene.

**Left Panel** (dark card with gold border):
- "JOIN THE GAME!" heading.
- Large QR Code.
- Instructions: "1. Scan the QR code / 2. Enter the Game Code / 3. Join and get ready!"
- "GAME CODE: 482731" in bold gold.
- "0 PLAYERS JOINED" counter (updates live) with group icon at bottom-left.

**Right Panel** (dark cards stacked):
- "🏆 BE THE FIRST — Earn Bonus Points!"
- "🏆 PLAY & WIN — Compete for glory!"
- "💎 HAVE FUN — Enjoy the game!"

**Bottom-right**: Chat bubble icon with text "Get ready! The game will start soon..."

---

### Screen 2 — Choose Your Team

**Background**: Fantasy tavern/dungeon interior — stone walls, torches, gothic arches, dramatic lighting.

**Header**: "COLOR KINGDOM" logo centered top. Below: "**CHOOSE YOUR TEAM**" in large bold gold.

**4 Team Banners** displayed side by side, each filling roughly 1/4 of the width:

| Team | Name | Color | Tagline | Character |
|------|------|-------|---------|-----------|
| 1 | **Queen of Hearts** | Red Shade | "Powerful, bold and born to rule." | Dark queen with red heart motifs |
| 2 | **Alice in Wonderland** | White Shade | "Curious, clever and full of wonder." | Alice (blue dress, flowers) |
| 3 | **Medusa** | Purple Shade | "Mysterious, cunning and fearless." | Medusa (snake hair, violet aura) |
| 4 | **Atlantis** | Blue Shade | "Strong, united and unstoppable." | Atlantis city/trident emblem |

Each banner:
- Tall card with ornate golden frame.
- Character illustration at top.
- Team name in bold gold.
- Color shade name in colored subtitle.
- Tagline in small italic text.
- "SAMPLE MEMBERS" section: 4 student avatar portraits with names.

**Bottom tagline**: "→ Pick a team. Earn points. Win glory! ←"

**Top-left**: QR code + "GAME CODE: 482731 — Share the code with your friends!"
**Top-right**: Small settings gear icon.

---

### Screen 3 — Team Reveal (per-team)
Similar to Screen 2 but focuses on one team at a time, cycling through all 4 teams for dramatic effect.

---

### Screen 4 — Welcome to Color Kingdom
(Brief cinematic intro screen before gameplay begins — 3–5 seconds.)
Fantasy background with the Color Kingdom logo large and centered with dramatic glow effects.

---

### Screen 5 — Game Initial State (Hex Map)

**Layout**: Landscape full-screen.

**Hex Grid**: A large hexagonal tile grid fills the center of the screen — initially all hexes are neutral gray/empty.

**4 Corner Regions**: Each team starts with a small cluster of their colored hexes in one corner:
- Top-left: Red (Queen of Hearts).
- Top-right: Purple (Medusa).
- Bottom-left: White/Cream (Alice).
- Bottom-right: Blue (Atlantis).

**Team Panels**: Each corner has a vertical card showing:
- Character portrait illustration.
- Castle/keep icon (team's base).
- List of team member names with small avatar circles.

**Bottom-center**: "Game PIN: 4709789" in small text.

**Color Kingdom logo** centered at top.

---

### Screen 6 — Game Fighting State (Active Gameplay)

Same hex map but now territory has expanded significantly. Teams have been answering questions and their colors spread outward across the hex grid, creating a patchwork territory map.

Team panels update to reflect current territory counts / score.

The hex tiles near borders between two teams show contested/blending areas.

---

### Screen 7 — Winner Proclamation

Same hex map view but one team's color (e.g., Red — Queen of Hearts) dominates nearly the entire map.

**Top-left**: Large announcement card — character portrait + "QUEEN OF HEARTS WINS!" in bold red with golden trophy.

Other teams' remaining territories shown in corners. Their castle icons are grayed out or "defeated."

**Color Kingdom logo** still at top center.

**Bottom**: Game PIN still visible.

---

## Game Rules — Color Kingdom

- **Team-based** (2–4 teams).
- Each team starts with a small hex territory in one corner.
- Each question: all teams answer simultaneously on their devices.
- **Correct team answer** → expand territory by conquering adjacent neutral hexes.
- **More correct team members** = more hexes gained this round.
- If two teams have both correct answers, faster team gets the contested hexes.
- Team that controls the **most hexes** when questions run out = WINNER.
- Alternatively, first team to capture a set percentage (e.g., 60%) of total hexes wins instantly.

### Hex Expansion Formula
```
hexes_gained = Math.floor(correct_count / total_team_members * max_hexes_per_round)
max_hexes_per_round = 5 (configurable by teacher)
```

---

## Frontend Architecture

```
ColorKingdomHostView
  ├── LobbyScreen (Screen 1)
  │     ├── CastleBackground
  │     ├── ColorKingdomLogo
  │     ├── WaitingText
  │     ├── LeftJoinPanel (QR + Game Code + Player Count)
  │     └── RightTipCards
  ├── TeamChooseScreen (Screen 2)
  │     ├── TeamBanner[] (4 banners, side-by-side)
  │     │     ├── CharacterIllustration
  │     │     ├── TeamName + ColorLabel
  │     │     ├── Tagline
  │     │     └── MemberAvatars[]
  │     └── GameCodePanel (top-left)
  ├── WelcomeIntroScreen (Screen 4, auto-advance)
  ├── HexMapScreen (Screens 5, 6, 7)
  │     ├── ColorKingdomLogo (top-center)
  │     ├── HexGrid
  │     │     └── HexTile[] (colored, conquerable, animated expansion)
  │     ├── TeamPanel[] (4 corners)
  │     │     ├── CharacterPortrait
  │     │     ├── CastleIcon
  │     │     └── MemberList[]
  │     └── GamePinFooter
  └── WinnerScreen (Screen 7)
        ├── WinnerAnnouncement (portrait + name + trophy)
        ├── HexMapFinalState
        └── DefeatedTeamPanels[]
```

### HexTile State
```js
{
  id: string,          // "hex-r3-c5"
  owner: "team1" | "team2" | "team3" | "team4" | null,
  color: string,       // hex color code
  isContested: boolean,
  animationState: "idle" | "capturing" | "captured"
}
```

---

## Backend Architecture

### API Endpoints
```
POST /api/games/colorkingdom/create
Body: { quizId: string, numberOfTeams: 2|3|4, hexGridSize: number }
Response: { gamePin, gameId, teams: Team[] }

GET  /api/games/colorkingdom/:gameId/hexmap
Response: { hexGrid: HexTile[] }

WS   /ws/games/:gameId
← Events: question_start, answer_reveal, hex_expansion_update, game_over
```

### Database Tables
```sql
hex_territories (
  id          UUID PRIMARY KEY,
  game_id     UUID REFERENCES game_sessions(id),
  hex_row     INTEGER,
  hex_col     INTEGER,
  owner_team  UUID REFERENCES pyramid_teams(id),
  captured_at TIMESTAMP
)
```

---

## Sound & Audio (Color Kingdom)

| Sound Slot | Trigger | Default |
|------------|---------|---------|
| Lobby Music | Waiting for players | Royal fanfare loop |
| Player Join | New student joins | Coin/bell sound |
| Team Selection | Player picks a team | Sword clang / battle horn |
| Game Start | Welcome screen | Epic orchestral swell |
| Question Reveal | Question appears | Dramatic horn |
| Correct Answer | Team answers correctly | Victory chime |
| Wrong Answer | Team answers wrong | Low thud |
| Hex Capture | Territory expands (animation) | Tile conquest sound |
| Territory Contested | Two teams fight for hex | Battle clash SFX |
| Win Condition Met | One team dominates | Grand victory anthem |
| Winner Proclamation | Final winner screen | Royal proclamation fanfare |

### Assigning Sounds (Settings → Sound → Color Kingdom)
Upload `.mp3`, `.wav`, or `.ogg` for each slot. Factory defaults used if not customized.
