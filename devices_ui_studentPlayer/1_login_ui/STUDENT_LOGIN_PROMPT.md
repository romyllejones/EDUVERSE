# EduVerse — Student Player Device Login UI Prompt

## Reference Image
`login.png`

---

## Network Requirement
> ⚠️ **Same WiFi / LAN Only.**
> The teacher's desktop and ALL student devices MUST be connected to the **same local WiFi network** (or school LAN).
> No internet connection is required. The app runs entirely on the local network.
> The teacher's computer acts as the local server. Students connect via the server's local IP address (e.g., `192.168.1.10:PORT`).

---

## Visual Design Prompt

Build the **student login / join game** screen for student devices (mobile phones, tablets, other PCs).

### Overall Style
- Full-screen portrait orientation.
- Deep dark navy background with faint star/space particle effect.
- A soft horizon glow at the very bottom (cyan/blue light emanating upward from the bottom edge).

### Header Section (Top)
- Circular glowing avatar: AI robot head silhouette in deep blue, surrounded by a bright cyan glowing ring. Positioned center-top.
- Below avatar: "**EduVerse**" brand text — "Edu" in white, "Verse" in cyan/blue.
- Subtitle: "You're almost in the game!" in white.
- Smaller text: "Please enter your details to join." in muted blue-gray.

### Game ID Display
- A dark rounded card/panel below the subtitle.
- Inside: "GAME ID" label in small cyan caps.
- Large bold white text: the Game ID code (e.g., "**A7B3K9**") with a small game controller icon to its left.

### Form Fields (stacked inside the card)

1. **STUDENT ID NUMBER** (text input)
   - Left icon: person silhouette.
   - Placeholder: "Enter your Student ID".
   - Example value: `2024-56789`.

2. **PASSWORD** (password input)
   - Left icon: padlock.
   - Right icon: eye toggle (show/hide).
   - Placeholder: "Enter your password".
   - **Default password = Student ID Number** (student can change after first login if enabled).

### Join Button
- Full-width, tall, rounded, vivid blue fill.
- Left icon: group/people icon.
- Bold white text: "JOIN GAME".
- Glowing subtle aura around the button.

### Help Text
- "OR" divider.
- "❓ Need help? Ask your teacher." in small muted text.

---

## Student Authentication Flow

```
Student opens browser on their device
→ Navigates to: http://[teacher's local IP]:[port]/join
  (e.g. http://192.168.1.10:3000/join?pin=A7B3K9)
        ↓
Join screen appears, Game ID pre-filled from URL
        ↓
Student enters:
  • Student ID (e.g. 2024-56789)
  • Password   (default = Student ID)
        ↓
Server checks credentials against uploaded Student Database
        ↓
  ✓ Match found → session created → redirect to Welcome Player screen
  ✗ No match   → "Incorrect Student ID or password. Ask your teacher."
```

---

## Student Database Schema (Uploaded by Teacher)

The teacher uploads a database of students. Each student record contains:

| Field | Type | Notes |
|-------|------|-------|
| **ID Number** | Text (required) | e.g. `2024-56789` — used as login username |
| **Thai Name** | Text (required) | Student's Thai full name |
| **English Name** | Text (required) | Student's English full name |
| **Section** | Text (required) | Class section, e.g. `STEM 1`, `Arts 2` |
| **Class Number** | Integer (required) | Student's class/seat number, e.g. `16` |
| **Password** | Text | Default = ID Number. Hashed before storage |
| **Status** | Boolean | Active / Inactive |

> All 5 core fields (ID Number, Thai Name, English Name, Section, Class Number) are stored in a **single unified database table** per class group. Multiple class groups can exist.

### CSV Upload Format
```csv
id_number,thai_name,english_name,section,class_number
2024-56789,นาย ฮวน เรเยส,Juan Miguel D. Reyes,STEM 1,16
2024-56790,นางสาว มาเรีย ซานโตส,Maria Santos,STEM 1,17
2024-56791,นาย อเล็กซ์ จอห์นสัน,Alex Johnson,Arts 2,1
```

---

## Frontend Architecture (Mobile-First)

```
StudentLoginPage
  ├── StarfieldBackground
  ├── HorizonGlow (bottom gradient)
  ├── AvatarCircle (glowing ring + robot silhouette)
  ├── BrandLogo ("EduVerse")
  ├── Headline ("You're almost in the game!")
  ├── Subtitle
  ├── GameIDCard (pre-filled from URL param ?pin=)
  ├── FormCard
  │     ├── StudentIDInput (text)
  │     └── PasswordInput (with eye toggle)
  ├── ErrorMessage (conditional, red)
  ├── JoinGameButton
  └── HelpText ("Need help? Ask your teacher.")
```

---

## Backend Architecture (Local Server — Same WiFi)

### Server Discovery
- The teacher's desktop runs the EduVerse server on a fixed local port (e.g., `3000`).
- The app displays the **local IP + port** prominently on the teacher dashboard so students know what URL to type: `http://192.168.1.10:3000`.
- Optionally: the lobby QR code encodes this URL so students just scan and connect.

### API Endpoint
```
POST /api/student/join
Body: {
  gamePin:   string,    // e.g. "A7B3K9"
  studentId: string,    // e.g. "2024-56789"
  password:  string     // plain text, server hashes and compares
}
Response 200: {
  sessionToken: string,
  playerName:   string,     // English name from DB
  thaiName:     string,
  section:      string,
  classNumber:  number,
  gameMode:     string,
  gameStatus:   "lobby" | "started"
}
Response 401: { message: "Incorrect Student ID or password." }
Response 404: { message: "Game not found or has ended." }
Response 409: { message: "You are already in this game." }
```

### Database Table
```sql
students (
  id             UUID PRIMARY KEY,
  database_id    UUID REFERENCES student_databases(id) ON DELETE CASCADE,
  id_number      TEXT NOT NULL,
  thai_name      TEXT NOT NULL,
  english_name   TEXT NOT NULL,
  section        TEXT NOT NULL,
  class_number   INTEGER NOT NULL,
  password_hash  TEXT NOT NULL,   -- bcrypt of id_number by default
  is_active      BOOLEAN DEFAULT TRUE,
  created_at     TIMESTAMP DEFAULT NOW(),
  UNIQUE(database_id, id_number)
)
```
