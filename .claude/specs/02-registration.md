# Spec: Registration

## Overview

Add working user registration to Spendly. Currently `GET /register` renders a blank shell template. This step wires up the form, adds a `POST /register` handler, writes the `create_user()` and `get_user_by_email()` helpers to `database/db.py`, and stores the new user with a hashed password. On success the user is redirected to `/login`. Inline errors are shown for duplicate email or missing fields, using Flask's `flash()` system (requires `SECRET_KEY` on the app).

---

## Depends on

- **Step 1 — Database Setup**: `get_db()`, `init_db()`, and the `users` table must already exist.

---

## Routes

- `GET /register` — Render the registration form — public  
- `POST /register` — Process form submission, create user, redirect to login — public

---

## Database changes

No new tables or columns. The `users` table (id, name, email, password_hash, created_at) already satisfies all requirements.

New helper functions in `database/db.py`:

- `get_user_by_email(email)` — returns the matching row or `None`
- `create_user(name, email, password)` — hashes the password and inserts a new row; returns the new user's `id`

---

## Templates

- **Modify:** `templates/register.html`
  - Add an HTML form with `method="POST"` and `action="{{ url_for('register') }}"`
  - Fields: `name` (text), `email` (email), `password` (password), `confirm_password` (password)
  - Display flashed error messages above the form
  - All links use `url_for()` — no hardcoded URLs

---

## Files to change

- `app.py` — add `POST` method to the `/register` route; import `flash`, `redirect`, `request`, `url_for`, `session` from flask; set `app.secret_key`; call `get_user_by_email()` and `create_user()`
- `database/db.py` — add `get_user_by_email()` and `create_user()`
- `templates/register.html` — add the registration form and flash message display

---

## Files to create

None.

---

## New dependencies

No new pip packages. `werkzeug.security.generate_password_hash` is already available.

---

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only — never f-strings or string concatenation in SQL
- Passwords hashed with `werkzeug.security.generate_password_hash` — never stored in plain text
- Use CSS variables — never hardcode hex values in templates or stylesheets
- All templates extend `base.html`
- DB logic stays in `database/db.py` — the route function calls helpers, never runs queries directly
- Use `flash()` for user-facing errors; use `abort()` for HTTP errors
- `app.secret_key` must be set before any `flash()` or `session` usage; use a hard-coded dev string for now (e.g. `"dev-secret-change-in-prod"`)
- Validate server-side: name non-empty, valid email format, password ≥ 8 characters, passwords match
- On duplicate email return the user to the form with a clear error message — do not crash
- On success redirect to `/login` with a success flash message

---

## Definition of done

- [ ] `GET /register` renders the form with name, email, password, and confirm-password fields
- [ ] Submitting valid data creates a new row in `users` with a hashed password (not plain text)
- [ ] Submitting a duplicate email shows an inline error and does not insert a second row
- [ ] Submitting mismatched passwords shows an inline error and does not insert any row
- [ ] Submitting blank required fields shows an inline error
- [ ] Successful registration redirects to `/login`
- [ ] No raw SQL strings are constructed with f-strings or `%` formatting
- [ ] `database/db.py` contains `get_user_by_email()` and `create_user()` — no query logic in `app.py`
- [ ] App starts and all existing routes still work after the change
