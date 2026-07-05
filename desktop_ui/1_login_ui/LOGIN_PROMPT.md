# EduVerse — Desktop Login UI Prompt

## Reference Image
`EduVerse Login UI.png`

---

## Visual Design Prompt

Build the desktop login screen for **EduVerse** exactly as shown in the reference image.

### Background
- Full-screen deep navy/dark blue background with faint circuit-board / digital-grid lines and particle stars scattered across the canvas.
- Ambient glow effects in the corners and behind the login card.
- 3D floating decorative elements arranged around the card:
  - Top-left: open 3D book with white/cream pages, blue cover.
  - Bottom-left: open 3D book with green cover.
  - Top-right: 3D hexagonal badge/shield with a gold star icon inside (blue-purple gradient shield).
  - Right-center: a partial view of a 3D Earth globe glowing green with teal atmosphere.
  - Small glowing blue dot/orb in the upper-right area.
- Bottom-right corner watermark: "Dola AI" in white text (production version replaces with EduVerse branding).

### Login Card (Center)
- Frosted glass card with soft dark-navy tint, rounded corners (~20px radius), subtle translucent border, and a soft drop shadow.
- Card is vertically centered, horizontally centered.
- **Logo/Brand Text**: "EduVerse" rendered in a bold gradient font — left half in deep blue, right half in vivid purple/violet. No icon beside the text.
- **Username Field**:
  - Dark rounded input (full card width inside padding).
  - Left icon: person/user silhouette icon in slate-gray.
  - Placeholder text: "Enter your username" in muted gray.
  - No label above the field.
- **Password Label**: Small white label "Password" above the password field.
- **Password Field**:
  - Same dark rounded input style as username.
  - Left icon: padlock icon in slate-gray.
  - Placeholder text: "Enter your password".
  - Eye toggle icon on the right to show/hide password.
- **Login Button**:
  - Full width, tall rounded button.
  - Vivid green fill (#2ECC40 or similar).
  - White bold text "Login" centered.
  - Subtle hover state: slightly brighter green + small scale lift.

---

## UI Behavior & Functionality

### Authentication Flow
1. Teacher/admin enters **Username** (Teacher ID format: `T852`, or admin: `admin`).
2. Teacher enters **Password** (default for new teachers: `DEFAULT`; admin default: `admin123`).
3. On submit:
   - Validate that both fields are non-empty.
   - Send credentials to local auth API endpoint.
   - If valid → create session → redirect to **Teacher Dashboard**.
   - If invalid → show inline error below the button: _"Invalid username or password. Please try again."_ (red text, no hard reload).
4. Password field supports show/hide toggle.
5. "Enter" key submits the form.

### Seeded Accounts
| Role | Username | Password | Auto-created |
|------|----------|----------|--------------|
| Administrator | `admin` | `admin123` | Yes (on first launch) |
| Teacher (example) | `T852` | `DEFAULT` | By admin |

### Security Notes
- Passwords must be stored hashed (bcrypt or argon2) in the database.
- Session token stored in a secure HTTP-only cookie or local session store.
- After 5 failed attempts, lock account for 10 minutes and show lockout message.
- Admin account is never deleted or recreated if it already exists.

---

## Frontend Architecture

```
LoginPage
  ├── Background (canvas/CSS animated particles + circuit lines)
  ├── FloatingDecorations (3D assets positioned absolutely)
  ├── LoginCard
  │     ├── BrandLogo ("EduVerse" gradient text)
  │     ├── UsernameInput (icon + text field)
  │     ├── PasswordLabel
  │     ├── PasswordInput (icon + text field + eye toggle)
  │     ├── ErrorMessage (conditional, red)
  │     └── LoginButton (green CTA)
  └── FooterWatermark
```

### State
```
{
  username: string,
  password: string,
  showPassword: boolean,
  isLoading: boolean,
  errorMessage: string | null,
  loginAttempts: number
}
```

---

## Backend Architecture

### API Endpoint
```
POST /api/auth/login
Body: { username: string, password: string }

Response 200: { token: string, role: "admin" | "teacher", name: string }
Response 401: { message: "Invalid username or password." }
Response 423: { message: "Account locked. Try again in X minutes." }
```

### Auth Middleware
- All protected routes require a valid session token.
- Role-based access: `admin` has all permissions; `teacher` has restricted access.

### Database Table
```sql
users (
  id          UUID PRIMARY KEY,
  username    TEXT UNIQUE NOT NULL,
  password_hash TEXT NOT NULL,
  role        TEXT NOT NULL DEFAULT 'teacher',   -- 'admin' | 'teacher'
  is_active   BOOLEAN DEFAULT TRUE,
  created_at  TIMESTAMP DEFAULT NOW(),
  last_login  TIMESTAMP
)
```

---

## Sound & Audio Behavior
- On successful login: play a short "chime/success" sound (assignable in Settings → Sound).
- On failed login: play a short "error/buzz" sound (assignable in Settings → Sound).
- All sounds are optional and respect the global mute setting.
