# Spendly

Spendly is a Flask-based expense tracker organized with a simple, beginner-friendly structure:
- `app.py` holds all routes in a single file
- `database/db.py` contains SQLite helper functions
- `templates/` stores Jinja templates that extend `base.html`
- `static/` stores CSS and JavaScript assets

## Architecture Diagram

```text
+-------------------+
|      Browser      |
| User visits page  |
+---------+---------+
          |
          v
+-------------------+
|      app.py       |
| Flask app + all   |
| routes in one     |
| file              |
+----+---------+----+
     |         |
     |         +--------------------------+
     |                                    |
     v                                    v
+-------------------+          +-------------------+
|    templates/     |          |  database/db.py   |
| base.html         |          | get_db()          |
| landing.html      |          | init_db()         |
| login.html        |          | seed_db()         |
| register.html     |          +---------+---------+
| privacy.html      |                    |
| terms.html        |                    v
+---------+---------+          +-------------------+
          |                    |  SQLite Database  |
          v                    +-------------------+
+-------------------+
|     static/       |
| css/              |
| js/               |
| images/ optional  |
+-------------------+
```

## Project Structure

```text
spendly/
|-- app.py                 # Main Flask app with all routes
|-- database/
|   |-- db.py              # SQLite helpers: get_db(), init_db(), seed_db()
|-- templates/
|   |-- base.html          # Shared layout; every page should extend this
|   |-- landing.html       # Landing page
|   |-- login.html         # Login page
|   |-- register.html      # Register page
|   |-- privacy.html       # Privacy page
|   |-- terms.html         # Terms page
|-- static/
|   |-- css/
|   |   |-- style.css      # Shared styles
|   |   |-- landing.css    # Landing page styles
|   |-- js/
|   |   |-- main.js        # Frontend JavaScript
|-- README.md
|-- requirements.txt
```

## How The App Works

1. The browser sends a request to a Flask route in `app.py`.
2. `app.py` decides which page or action to return.
3. If a page is needed, Flask renders a template from `templates/`.
4. Templates inherit the shared layout from `templates/base.html`.
5. CSS and JavaScript are loaded from `static/`.
6. When database support is added, `app.py` will call functions from `database/db.py`.
7. `database/db.py` will interact with the SQLite database.

## Design Principles

- Simple structure for learning and small projects
- No blueprints; routing stays in one file
- Shared base template for consistent UI
- Database logic separated from route logic
- Static assets grouped by type for easy maintenance

## Current Status

- `app.py` already contains the main routes
- `templates/` already contains the core pages
- `static/` already contains CSS and JS assets
- `database/db.py` is still a placeholder and can be implemented next

## Code Style

This project follows a simple, beginner-friendly code style:

- Python uses `snake_case` for function names and variables
- All Flask routes are kept in a single file: `app.py`
- No blueprints are used to keep the structure easy to understand
- Templates use a shared layout with `base.html`
- Static asset folders and file names use lowercase naming
- Database logic is separated into `database/db.py`
- HTML, CSS, and JavaScript are organized into their own folders
- The structure is kept simple and readable for learning purposes

### Style Used In This Project

- Pythonic naming style
- Basic `PEP 8`-friendly formatting
- Single-file Flask application structure
- Template inheritance pattern
- Separation of concerns between routes, database, templates, and static files

## Technical Constraints

This project follows these technical constraints:

- The Flask application must use a single `app.py` file for all routes
- Blueprints are not used
- All database helper functions must be placed in `database/db.py`
- SQLite is the database for this project
- Templates must be stored inside `templates/`
- All page templates must extend `base.html`
- Static files must be stored inside `static/`
- CSS files should go in `static/css/`
- JavaScript files should go in `static/js/`
- The project structure should stay simple and beginner-friendly
- Route logic, database logic, templates, and static assets should remain separated
