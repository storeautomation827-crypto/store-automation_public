# Store Automation v1.01

A Flask web app built to reduce the manual work of entering daily store data into Excel.

Staff can submit various business inputs from a browser, review the data, and push it to existing Excel files —
with a dry-run preview and a test-write step before touching production.

## Background

At the restaurant where I work part-time, daily operational data is managed in Excel.
The entries are simple but repetitive, and I wanted to see if a web form could make the workflow smoother.

The core constraint was that the existing Excel files had to stay exactly as-is.
So instead of replacing them, I built this as a helper tool: browser input → SQLite → review → Excel.

Before this, I built a LINE Bot + OCR tool that reads receipt images and fills in Excel automatically.
That worked, but as the number of input fields grew, the message-based flow became awkward.
Rebuilding as a Flask app made the interface much easier to extend.

→ Previous project: [store-automation-ocr](https://github.com/storeautomation827-crypto/store-automation-ocr)

## System Architecture

```
[Browser / Smartphone]
          ↓  HTTP
  [Flask App (routes.py)]
          ↓
  [Service Layer (db.py / write_service.py)]
          ↓                       ↓
    [SQLite DB]           [Excel Layer]
  (input_history)   backup.py / path_guard.py / writer.py
                                  ↓
                          [Excel Files]
```

It runs on Flask and SQLite only, so the store PC just needs Python and three packages.

## Features

### Input Forms

Multiple types of business input forms are available from the browser.
Each form type has its own validation logic (numeric fields, required text fields, etc.)
before saving to SQLite. Form types and field details are managed via system config
and are not disclosed in this repository.

### Data Management
- All submitted forms are saved to the `input_history` SQLite table with `pending` status
- `/history` shows the full input log
- `/review` shows pending records and a dry-run preview of what would be written to Excel

### Excel Write Flow
1. `GET /review/plan` — JSON preview of the planned write; no files are touched
2. `POST /review/write_test` — write to a test copy of the target Excel file
3. Review the result on screen (written cells, skipped items, backup path)
4. Push to production Excel when confirmed; `status` updates to `reflected`

### Authentication and Roles
- Session-based login authentication
- Multiple permission roles are supported (role names and details are not disclosed)
- Available features vary by role

### Safety Design

Three deliberate constraints on the write path:

**PathGuard (write path validation)**
Writes are only allowed within a caller-specified `allowed_root` directory.
Paths are normalized with `Path.resolve()` before comparison, which prevents `../` traversal.
Files matching the backup naming pattern (`_backup_YYYYMMDD_HHMMSS`) or Excel temp files (`~$`) are always rejected.

**Mandatory backup**
A backup copy is always created before writing.
If the copy fails, the write is aborted.
Naming convention: `original_filename_backup_YYYYMMDD_HHMMSS.xlsx`

**Test copy first**
Writes go to a pre-made test copy before touching the production file.
The result is shown on screen so the content can be verified before the final push.

## Database Schema

One table:

```sql
CREATE TABLE input_history (
    id            INTEGER PRIMARY KEY AUTOINCREMENT,
    store_id      TEXT    NOT NULL,
    business_date TEXT    NOT NULL,        -- YYYY-MM-DD
    input_type    TEXT    NOT NULL,        -- input category (managed via config)
    data_json     TEXT,                   -- input payload as JSON
    created_by    TEXT,
    created_at    TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status        TEXT    DEFAULT 'pending'  -- pending / reflected
);
```

The shape of `data_json` varies by input type, but keeping one table kept things simple.
Validation is handled in Python (`db.py`) rather than database constraints.

## Data Flow

```
1. Staff logs in from browser
2. Fill in a business input form
3. Data is validated and saved to SQLite as "pending"
4. Open /review to see pending records and a write plan preview
5. Run a test write to the test Excel copy (/review/write_test)
6. Check the result: written cells, skipped items, backup path
7. Push to production Excel when ready
8. Status updates from "pending" to "reflected"
```

## Directory Structure

```
store_automation_v1_01/
├── app/
│   ├── auth/
│   │   └── login.py            # Login, password check, session management
│   ├── excel/
│   │   ├── backup.py           # File copy only; never modifies source
│   │   ├── path_guard.py       # Whitelist-based write path validation
│   │   ├── writer.py           # openpyxl cell write logic
│   │   ├── write_plan.py       # Write plan generation, summary, review format
│   │   ├── scanner.py          # Excel file scanner
│   │   └── mappings.py         # Cell mapping config loader
│   ├── services/
│   │   ├── db.py               # SQLite ops, validation, preview formatting
│   │   ├── write_service.py    # Test write execution
│   │   └── pending_to_write_records.py
│   └── web/
│       └── routes.py           # Flask Blueprint routes
├── config/
│   ├── stores.json.example
│   └── users.json.example
├── templates/                  # HTML templates
├── static/                     # CSS and JavaScript
├── tests/                      # 21 test files
├── scripts/                    # Dev and inspection scripts
├── run.py                      # Flask entry point
└── requirements.txt
```

## Tech Stack

| Category | Tech |
|---|---|
| Backend | Python 3.12 / Flask 3.0 |
| Database | SQLite3 (Python stdlib) |
| Frontend | HTML / CSS / JavaScript |
| Excel | openpyxl 3.1 |
| Testing | unittest / pytest |

`requirements.txt` has only three packages: Flask, Werkzeug, and openpyxl.

## Setup

```bash
git clone https://github.com/storeautomation827-crypto/store-automation_public.git
cd store-automation_public

python -m venv venv
venv\Scripts\activate    # Windows
# source venv/bin/activate  # macOS / Linux

pip install -r requirements.txt

cp config/stores.json.example config/stores.json
cp config/users.json.example config/users.json

python run.py
```

## Running Tests

21 test files covering DB operations, Excel writing, path guard, and routing.

```bash
pytest tests/
```

Tests always use a temporary SQLite database and are designed so they never read or write production Excel files.

## What I Want to Add

- Enable the production Excel write button (currently test copy only)
- Better mobile UX
- Multi-store support
- Admin log viewer
- Windows launcher app for store staff

## What's Not in This Repo

This repo has the Flask app, SQLite logic, Excel handling, and tests.
Real store names, actual business data, production Excel files, input type details,
and store-specific config are not included.

## License

Published for portfolio and learning purposes.
