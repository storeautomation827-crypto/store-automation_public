
# Store Automation v1.01

A Flask-based web application for streamlined store operations management and business reporting automation.

## Overview

Store Automation is a comprehensive framework for managing daily store operations, expense tracking, and business intelligence. Built with Python and Flask, it provides an intuitive web interface for staff to input critical business metrics, while the backend orchestrates data processing, validation, and Excel-based reporting workflows.

This repository contains the core infrastructure and foundational systems that power the automation platform—designed with scalability and extensibility in mind for future service deployments.

## Key Features

- **Secure Authentication & Authorization**: User management with role-based access control
- **Multi-Format Data Input**: Web-based forms for daily reports, expense tracking, and performance metrics
- **Excel Integration**: Seamless export and append operations to Excel workbooks
- **Data Validation & Path Security**: Robust input validation and file system safety mechanisms
- **Comprehensive Test Coverage**: Unit and integration tests for critical business logic
- **Modular Architecture**: Clean separation of concerns for easy extension and maintenance

## Tech Stack

- **Backend**: Python 3.12, Flask
- **Database**: SQLite
- **Frontend**: HTML5, CSS3, JavaScript (Vanilla)
- **Testing**: pytest
- **Data Processing**: openpyxl, pandas

## Project Structure

```
store-automation/
├── app/
│   ├── auth/              # Authentication & user management
│   ├── excel/             # Excel file operations (read/write/backup)
│   ├── services/          # Business logic & database operations
│   ├── web/               # Flask blueprints & route handlers
│   └── __init__.py        # App factory pattern
├── templates/             # Jinja2 HTML templates
├── static/
│   ├── css/               # Stylesheets
│   └── js/                # Frontend logic
├── tests/                 # Unit & integration tests
├── scripts/               # Utility scripts
├── config/                # Configuration files (see below)
├── requirements.txt       # Python dependencies
└── run.py                 # Application entry point
```

## Getting Started

### Prerequisites

- Python 3.10+
- pip package manager
- Windows, macOS, or Linux

### Installation

1. Clone the repository:
```bash
git clone https://github.com/storeautomation827/store-automation.git
cd store-automation
```

2. Create a virtual environment:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Configure your environment:
```bash
# Copy the example configuration files
cp config/stores.json.example config/stores.json
cp config/users.json.example config/users.json
```

5. Initialize the application:
```bash
python run.py
```

The application will start on `http://localhost:5000`

## Configuration

### stores.json

Define your store locations and metadata:

```json
{
  "stores": [
    {
      "id": "store_id_1",
      "name": "Store Name",
      "active": true
    }
  ]
}
```

### users.json

Manage user credentials and permissions:

```json
{
  "users": [
    {
      "username": "admin",
      "password_hash": "hashed_password_here",
      "role": "admin"
    }
  ]
}
```

> **Security Note**: Passwords should be properly hashed using werkzeug.security or similar. Never commit credentials to version control.

## Core Modules

### Authentication (`app/auth/`)
- User login and session management
- Password validation and security
- User file persistence

### Excel Operations (`app/excel/`)
- **Scanner**: Reads and parses Excel workbooks
- **Writer**: Writes data to Excel workbooks with cell-level control
- **Backup**: Automated backup of critical Excel files
- **Path Guard**: Validates file paths to prevent directory traversal attacks

### Services (`app/services/`)
- **Database**: SQLite ORM operations and schema initialization
- Business logic for data aggregation and reporting

### Web Routes (`app/web/`)
- RESTful endpoints for form submissions
- Data input and retrieval
- Dynamic reporting

## Testing

Run the test suite:

```bash
pytest tests/
```

Run with coverage:

```bash
pytest --cov=app tests/
```

Current test coverage includes:
- Database operations and schema
- Excel file reading/writing
- Path security and validation
- Route handlers and form processing

## Development Workflow

1. Create a feature branch: `git checkout -b feature/your-feature`
2. Make your changes and write tests
3. Run `pytest` to ensure all tests pass
4. Commit with clear messages: `git commit -m "feat: description"`
5. Push and open a pull request

## Future Roadmap

- Multi-database backend support (PostgreSQL, MySQL)
- REST API expansion for third-party integrations
- Real-time dashboard and analytics
- Mobile app integration
- Cloud deployment templates
- Advanced reporting and forecasting
- Multi-language support

## Architecture Notes

This codebase demonstrates:
- **Clean Architecture**: Separation between business logic, persistence, and presentation
- **Security-First Design**: Input validation, path safety, and authenticated access
- **Testability**: Modular design enables comprehensive unit testing
- **Scalability**: Factory pattern and blueprint-based routing for easy feature addition

## Limitations & Disclaimers

This is a demonstration of core automation infrastructure. Certain operational details, store-specific configurations, and real-world data have been abstracted or removed for privacy and business protection.

For production deployment:
- Implement proper secret management (environment variables, HashiCorp Vault)
- Add comprehensive logging and monitoring
- Deploy with production-grade web server (Gunicorn, uWSGI)
- Implement rate limiting and request validation
- Enable HTTPS and CORS policies

