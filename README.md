# Store Automation v1.01

A Flask web app I built to reduce the manual work of entering daily store data into Excel.

Staff can submit daily reports, expenses, loss records, and petty cash from a browser,
review the data, and push it to existing Excel files — with a test-write step before touching production.

## Background

At the restaurant where I work part-time, daily sales, expenses, and other data are all managed in Excel.
The entries are simple but repetitive, and I wanted to see if a web form could make the workflow a bit smoother.

The core constraint was that the existing Excel files had to stay exactly as-is.
So instead of replacing them, I built this as a helper tool: browser input → SQLite → review → Excel.

Before this, I built a LINE Bot + OCR tool that reads receipt images and fills in Excel automatically.
That project worked, but as the number of input fields grew, the back-and-forth message flow became awkward.
Rebuilding it as a Flask app made the interface much easier to extend.

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
- **Daily report**: net sales, group count, customer count
- **Business report**: free text
- **Expenses**: ingredients (skewers, processed food, produce), drinks, supplies
- **Loss records**: cooking loss, disposal, trial batches, order mistakes, etc.
- **Petty cash**: payee, item name, amount in/out (item categories loaded from mapping config)

### Data Management
- All submitted forms are saved to the `input_history` SQLite table with `pending` status
- `/history` shows the full input log
- `/review` shows pending records and a dry-run preview of what would be written to Excel

### Excel Write Flow
1. `GET /review/plan` — JSON preview of the planned write; no files are touched
2. `POST /review/write_test` — write to a test copy of the target Excel file
3. Review the result on screen (written cells, skipped items, backup path)
4. Push to production Excel when confirmed; `status` updates to `reflected`

### Safety Design

Three deliberate constraints on the write path:

**PathGuard (write path validation)**
Writes are only allowed within a caller-specified `allowed_root` directory.
Paths are normalized with `Path.resolve()` before comparison, which prevents `../` traversal.
Files matching the backup naming pattern (`_backup_YYYYMMDD_HHMMSS`) or Excel temp files (`~$`) are always rejected as write targets.

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
    input_type    TEXT    NOT NULL,        -- daily_report, petty_cash, etc.
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
2. Fill in a form (daily report, expense, loss, etc.)
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

Open http://localhost:5000 in your browser.

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
Real store names, actual sales data, production Excel files, and store-specific config are not included.

## License

Published for portfolio and learning purposes.
