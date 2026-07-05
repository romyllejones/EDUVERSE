# EduVerse — Network Architecture (Same WiFi / LAN)

---

## Overview

EduVerse runs **entirely on a local network**. No internet connection is required during gameplay.

- The **teacher's desktop computer** is the **server**.
- **Student devices** (phones, tablets, other laptops) connect as **clients** via the same WiFi router or LAN switch.
- All communication happens inside the local network — no cloud, no external APIs during game sessions.

---

## Network Topology

```
                        ┌─────────────────────┐
                        │   WiFi Router / LAN  │
                        │   (School network)   │
                        └──────────┬──────────┘
                                   │
              ┌────────────────────┼────────────────────┐
              │                    │                    │
     ┌────────▼────────┐  ┌────────▼────────┐  ┌────────▼────────┐
     │  Teacher PC     │  │  Student Phone  │  │  Student Phone  │
     │  (SERVER)       │  │  (CLIENT)       │  │  (CLIENT)       │
     │  192.168.1.10   │  │  192.168.1.21   │  │  192.168.1.22   │
     │  Port: 3000     │  │  Browser only   │  │  Browser only   │
     └─────────────────┘  └─────────────────┘  └─────────────────┘
```

---

## How Students Connect

### Step 1 — Teacher starts the game
The teacher's EduVerse desktop app shows:
- **Local IP Address** prominently on the lobby screen (e.g., `192.168.1.10:3000`).
- **QR Code** that encodes the join URL: `http://192.168.1.10:3000/join?pin=A7B3K9`.
- **Game PIN** (6-digit code) as a text fallback.

### Step 2 — Students connect
Students on the same WiFi open their browser and either:
- Scan the QR code (preferred — no typing needed), OR
- Manually type the URL shown on the teacher's screen.

### Step 3 — Student logs in
- Student enters their **Student ID** and **Password** (default = Student ID).
- Server validates against the uploaded student database.
- Authenticated student appears in the teacher's lobby player list.

---

## Technical Stack (Local Server)

| Layer | Technology | Notes |
|-------|-----------|-------|
| Application Framework | Node.js + Express | Runs on teacher's machine |
| Real-Time Communication | Socket.io (WebSocket) | Same LAN, very low latency |
| Database | SQLite (local file) | No DB server required |
| Frontend (Teacher) | React + Vite | Served by Express |
| Frontend (Student) | React (mobile-optimized) | Served by same Express |
| QR Code Generation | qrcode npm library | Auto-generates join QR |
| Local IP Detection | Auto-detected on startup | Shown to teacher |

---

## Server Startup Behavior

When the teacher launches EduVerse, the app:
1. Starts the Express HTTP server on a configured port (default: `3000`).
2. Detects the machine's local IP address automatically.
3. Displays the full join URL and QR code on the dashboard.
4. Initializes SQLite database (creates tables if first run).
5. Seeds the admin account (`admin` / `admin123`) if not already seeded.

```
EduVerse Server Starting...
✓ Database initialized
✓ Admin account ready
✓ Server running at: http://192.168.1.10:3000
✓ QR Code generated for: http://192.168.1.10:3000/join
```

---

## WebSocket Architecture (Socket.io)

All game state is synchronized via WebSocket events on the local network.

### Rooms / Namespaces
```
/teacher    → teacher control namespace (1 connection per game)
/student    → student player namespace (N connections per game)
```

### Game Room Structure
```
room: "game:A7B3K9"  (keyed by Game PIN)
  ├── teacher socket (1)
  └── student sockets (N)
```

### Event Flow
```
Teacher emits:  game:start          → all students receive
Teacher emits:  question:reveal     → all students receive question
Student emits:  answer:submit       → server processes, updates game state
Server emits:   answer:reveal       → teacher + all students receive result
Server emits:   leaderboard:update  → teacher + all students receive new scores
Teacher emits:  game:end            → all students disconnected from room
```

---

## Latency Expectations (Same WiFi)

| Action | Expected Latency |
|--------|-----------------|
| Student joins lobby | < 200ms |
| Question broadcast to all students | < 100ms |
| Answer submission to server | < 50ms |
| Score update to teacher screen | < 150ms |
| Max recommended concurrent students | ~60 (limited by WiFi, not software) |

---

## Offline / No-Internet Behavior

- Entire app works with zero internet.
- No CDN assets (all JS/CSS bundled locally).
- No external font loading (fonts bundled or system fonts used as fallback).
- QR code is generated server-side (no Google Charts API).
- Game sounds are served from local file system.

---

## Port Configuration (Settings → Network)

The teacher can configure:

| Setting | Default | Notes |
|---------|---------|-------|
| Server Port | `3000` | Change if port conflicts |
| WebSocket Path | `/ws` | Auto-configured |
| CORS Allow List | `* (local only)` | Restricts to LAN automatically |
| Auto-detect IP | ON | Can manually override |

---

## Security Notes (LAN Context)

- No authentication tokens are sent to the internet.
- All student sessions are scoped to the game PIN and local session.
- The admin password should be changed from the default (`admin123`) before school use.
- Student passwords default to their ID number; teachers are encouraged to keep the network physically secured (school WiFi, not public hotspot).
- Sessions expire when the teacher closes the game or restarts the server.
