# Store Automation v1.01

A Flask-based web application for supporting daily store operations.

## Overview

Store Automation is a web application designed to make daily store work easier to manage.

The application allows staff to enter daily reports, expenses, and sales-related information through a browser. The entered data is stored in SQLite and can later be reflected in Excel files used for store operations.

The goal of this project is not to replace the existing Excel workflow completely, but to support it with a simple web interface and reduce manual input work.

## What This Application Does

- Provides a login-based web interface
- Allows staff to enter daily reports and expense information
- Stores submitted data in SQLite
- Shows input history for confirmation
- Reflects stored data into Excel files
- Creates backups before writing to Excel files
- Separates the main application logic into modules for easier maintenance

## Basic Workflow

1. A staff member logs in
2. The staff member selects the type of input
3. Daily report, expense, or related store information is entered
4. The data is saved to SQLite
5. The saved data can be reviewed later
6. The data can be reflected in an Excel file
7. A backup is created before the Excel file is updated

## Tech Stack

| Category | Technology |
|----|----|
| Backend | Python 3.12 / Flask |
| Database | SQLite |
| Frontend | HTML / CSS / JavaScript |
| Excel Handling | openpyxl |
| Data Processing | pandas |
| Testing | pytest |

## Project Structure

The main files and folders are organized as follows.

- `app/`
  - Main Flask application code.
  - Authentication, Excel handling, database logic, and web routes are separated here.

- `app/auth/`
  - Login, user management, and session-related logic.

- `app/excel/`
  - Excel reading, writing, and backup-related logic.

- `app/services/`
  - SQLite operations and data handling logic.

- `app/web/`
  - Flask routes and form submission handling.

- `templates/`
  - HTML templates used for page rendering.

- `static/`
  - CSS and JavaScript files used on the frontend.

- `tests/`
  - Test code for database operations, Excel writing, and form handling.

- `scripts/`
  - Utility scripts for development and checking behavior.

- `config/`
  - Configuration files for stores and users.
  - Actual operation-specific configuration files are managed separately from the public repository.

- `requirements.txt`
  - Python packages required to run the application.

- `run.py`
  - Entry point for starting the Flask application.

## Getting Started

### Requirements

- Python 3.10 or later
- pip
- Windows, macOS, or Linux

### 1. Clone the Repository

```bash
git clone https://github.com/storeautomation827-crypto/store-automation_public.git
```

```bash
cd store-automation_public
```

### 2. Create and Activate a Virtual Environment

```bash
python -m venv venv
```

For Windows:

```bash
venv\Scripts\activate
```

For macOS / Linux:

```bash
source venv/bin/activate
```

### 3. Install Dependencies

```bash
pip install -r requirements.txt
```

### 4. Prepare Configuration Files

Copy the example configuration files as needed.

```bash
cp config/stores.json.example config/stores.json
```

```bash
cp config/users.json.example config/users.json
```

For Windows Command Prompt:

```cmd
copy config\stores.json.example config\stores.json
```

```cmd
copy config\users.json.example config\users.json
```

### 5. Run the Application

```bash
python run.py
```

After starting the application, open the following URL in your browser.

```text
http://localhost:5000
```

## Configuration Files

### stores.json

This file defines store information.

```json
{
  "stores": [
    {
      "id": "store_001",
      "name": "Sample Store",
      "active": true
    }
  ]
}
```

Main fields:

- `id`
  - Store identifier.

- `name`
  - Display name of the store.

- `active`
  - Whether the store is active.

### users.json

This file defines user login information.

```json
{
  "users": [
    {
      "username": "admin",
      "password_hash": "hashed_password",
      "role": "admin"
    }
  ]
}
```

Main fields:

- `username`
  - Username used for login.

- `password_hash`
  - Hashed password.

- `role`
  - User role.

### Notes on Configuration

- Passwords should be hashed before use.
- Do not commit plain text passwords.
- Operation-specific settings and data should not be committed to GitHub.

## Core Features

### Authentication

- User login
- Session management
- Role-based access control

### Input Forms

- Daily report input
- Expense input
- Business report input
- Input confirmation

### Data Storage

- Saves submitted data to SQLite
- Allows input history to be reviewed
- Keeps data ready for later Excel reflection

### Excel Integration

- Reflects saved data into Excel files
- Creates backups before writing
- Checks the target file before writing

### Testing

- Database operation tests
- Excel reading and writing tests
- Form handling tests
- Main business logic tests

## Running Tests

Run all tests:

```bash
pytest tests/
```

Run tests with coverage:

```bash
pytest --cov=app tests/
```

## Scope of This Repository

This repository contains the main structure of the Flask application, input and storage flow, Excel integration, backup handling, and test code.

The following items are not included:

- Real store names
- Real sales data
- Actual Excel files used in operation
- Store-specific cell mappings
- Operation-specific configuration files

## Future Improvements

- Improve expense and petty cash input features
- Improve the input history screen
- Expand Excel reflection targets
- Add stronger admin review features
- Support multiple stores
- Improve role management
- Consider cloud deployment

## Notes

This repository is shared for educational and development purposes. It does not include actual store data or operation-specific files.

For production use, additional setup would be required, such as:

- Secret management using environment variables
- HTTPS support
- Production web server setup
- Logging and access control
- Regular data backup
