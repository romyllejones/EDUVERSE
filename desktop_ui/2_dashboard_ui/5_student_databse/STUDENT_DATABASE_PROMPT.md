# EduVerse — Students Database (Desktop / Teacher View) UI Prompt

## Reference Image
`student_database.png`

---

## Visual Design Prompt

Build the **Students Database** module exactly as shown in the reference image.

### Layout
- Same dark navy dashboard: fixed left sidebar + top header + scrollable main content.
- "STUDENTS DATABASE" heading in white at top-left of the main content area.
- **"+ Add Database"** — blue/cyan filled button with a "+" prefix, top-left below heading.
- Section label: "DATABASES LIST" in muted uppercase text.

### Database Cards Grid
- 3-column responsive grid (same card style as Available Quizzes).
- Each card:
  - Dark navy background, rounded corners, subtle border.
  - **Card title** (bold white): e.g., "Grade 10 – STEM 1"
  - **Student count** (group icon): e.g., "28 Students"
  - **Created date** (muted text): e.g., "Created: May 10, 2026"
  - **Top-right**: hamburger menu icon (3 lines) + close (X) button.

### Card Dropdown Menu (on hamburger click)
- **Edit Database** (pencil icon)
- **Add / Remove / Edit Students** (person icon)

---

## Student Record Fields (in One Database)

Each student database group contains records with these **5 required fields** plus password:

| # | Field | Type | Example | Notes |
|---|-------|------|---------|-------|
| 1 | **ID Number** | Text | `2024-56789` | Login username — must be unique within database |
| 2 | **Thai Name** | Text | `นาย ฮวน เรเยส` | Thai full name |
| 3 | **English Name** | Text | `Juan Miguel D. Reyes` | English full name |
| 4 | **Section** | Text | `STEM 1` | Class section label |
| 5 | **Class Number** | Integer | `16` | Student's class/seat number |
| — | **Password** | Text (hashed) | *(auto-set)* | Default = ID Number |
| — | **Status** | Boolean | Active | Active / Inactive |

> All fields live in a **single flat table per database group**. No separate tables per section — sections are just a field value and are filtered/grouped in reports.

---

## Functionality

### Creating a New Database Group
1. Click "+ Add Database".
2. Modal: enter **Database Name** (e.g., "Grade 10 – STEM 1") + optional description.
3. Confirm → empty database card appears.

### Adding Students — Three Methods

#### Method 1: Manual Entry
Form with all 5 fields + optional custom password.

#### Method 2: CSV Upload (Primary Method) ⭐
Upload a `.csv` or `.xlsx` file. Required column headers:

```csv
id_number,thai_name,english_name,section,class_number
2024-56789,นาย ฮวน เรเยส,Juan Miguel D. Reyes,STEM 1,16
2024-56790,นางสาว มาเรีย ซานโตส,Maria Santos,STEM 1,17
2024-56791,นาย อเล็กซ์ จอห์นสัน,Alex Johnson,Arts 2,1
```

- Default password for all imported students = their `id_number`.
- Duplicate `id_number` within the same database → skipped with a warning.
- After import: summary shown: "✓ 28 imported  ⚠ 2 skipped  ✗ 0 errors".
- **Template download link** available for teachers to fill and re-upload.

#### Method 3: Add Row in Table
Click "+ Add Student" at bottom of the student table view → inline row input.

### Student Table View (within a database)

Accessed via hamburger → "Add / Remove / Edit Students".

| Column | Sortable | Filterable |
|--------|----------|------------|
| ID Number | Yes | Yes |
| Thai Name | Yes | Yes |
| English Name | Yes | Yes |
| Section | Yes | Yes (dropdown filter) |
| Class Number | Yes | Yes |
| Status | — | Yes (Active/Inactive toggle) |
| Actions | — | Edit / Delete buttons |

- **Search bar**: searches across ID, Thai name, English name simultaneously.
- **Section filter dropdown**: shows only students of a selected section.
- **Bulk actions**: select multiple rows → bulk delete, bulk activate/deactivate.

---

## Frontend Architecture

```
StudentDatabasePage
  ├── PageHeader ("STUDENTS DATABASE" + Add Database button)
  ├── DatabaseGrid
  │     └── DatabaseCard[]
  │           ├── CardTitle
  │           ├── StudentCount
  │           ├── CreatedDate
  │           ├── MenuButton → Dropdown
  │           │     ├── EditDatabase → EditDatabaseModal
  │           │     └── ManageStudents → StudentTableView
  │           └── DeleteButton (X) → ConfirmModal
  ├── AddDatabaseModal
  │     ├── NameInput
  │     ├── DescriptionInput (optional)
  │     └── CreateButton
  └── StudentTableView (full-page or slide-over panel)
        ├── TableHeader (database name + student count)
        ├── ActionBar
        │     ├── SearchInput
        │     ├── SectionFilterDropdown
        │     ├── CSVUploadButton → FilePickerModal
        │     ├── TemplateDownloadLink
        │     └── AddStudentButton (manual)
        ├── StudentTable
        │     ├── TableHead (ID, Thai Name, English Name, Section, Class No, Status, Actions)
        │     └── StudentRow[] (inline edit + delete)
        └── BulkActionBar (appears when rows selected)
```

---

## Backend Architecture

### API Endpoints
```
GET    /api/students/databases
Response: { databases: [{ id, name, studentCount, createdAt }] }

POST   /api/students/databases
Body:  { name: string, description?: string }

PUT    /api/students/databases/:id
DELETE /api/students/databases/:id   (cascades to all students in it)

GET    /api/students/databases/:id/students
Query: ?search=&section=&status=active|inactive
Response: { students: Student[], sections: string[] }

POST   /api/students/databases/:id/students
Body:  { idNumber, thaiName, englishName, section, classNumber, password? }

PUT    /api/students/databases/:id/students/:studentId
Body:  (any subset of the above fields)

DELETE /api/students/databases/:id/students/:studentId

POST   /api/students/databases/:id/import
Body:  multipart/form-data { file: .csv or .xlsx }
Response: {
  imported: number,
  skipped: number,
  errors: [ { row: number, message: string } ]
}

GET    /api/students/databases/import-template
Response: text/csv  (downloadable template file)

POST   /api/students/databases/:id/students/bulk-action
Body:  { action: "activate"|"deactivate"|"delete", studentIds: string[] }
```

### Database Tables
```sql
student_databases (
  id          UUID PRIMARY KEY,
  name        TEXT NOT NULL,
  description TEXT,
  created_by  UUID REFERENCES users(id),
  created_at  TIMESTAMP DEFAULT NOW()
)

students (
  id            UUID PRIMARY KEY,
  database_id   UUID REFERENCES student_databases(id) ON DELETE CASCADE,
  id_number     TEXT NOT NULL,
  thai_name     TEXT NOT NULL,
  english_name  TEXT NOT NULL,
  section       TEXT NOT NULL,
  class_number  INTEGER NOT NULL,
  password_hash TEXT NOT NULL,   -- bcrypt(id_number) by default
  is_active     BOOLEAN DEFAULT TRUE,
  created_at    TIMESTAMP DEFAULT NOW(),
  UNIQUE(database_id, id_number)
)
```

### Password Default Logic
```
On import or manual create without explicit password:
  password_hash = bcrypt(id_number)

Student can change password only if teacher enables it in Settings.
If teacher resets a password: password_hash = bcrypt(id_number)
```
