# Spec: Login and Logout

## Overview

Wire up real authentication for Spendly. `GET /login` currently renders a static form, `POST /login` does not exist, and `GET /logout` is a placeholder string. This step adds a `POST /login` handler that verifies credentials against the hashed password stored at registration, opens an authenticated session via Flask's `session`, and redirects the user to `/profile`. `GET /logout` clears the session and redirects back to `/login`. Inline errors for bad credentials are shown via `flash()`. After this step the auth loop — register → login → logout — is end-to-end functional.

---

## Depends on

- **Step 1 — Database Setup**: `users` table and `get_db()` exist.
- **Step 2 — Registration**: `create_user()` and `get_user_by_email()` exist in `database/db.py`; `app.secret_key` is already set; users with hashed passwords exist in the DB.

---

## Routes

- `POST /login` — Verify credentials, set `session["user_id"]`, redirect to `/profile` — public
- `GET /logout` — Clear the session and redirect to `/login` — logged-in (no-op if already logged out)

The existing `GET /login` route stays as-is but will read flashed messages.

---

## Database changes

No new tables, columns, or constraints. `get_user_by_email()` from Step 2 is reused to look up the user by email; password verification uses `werkzeug.security.check_password_hash` against the stored `password_hash`.

No new helper functions are required in `database/db.py`. (Optional: a thin `verify_credentials(email, password)` wrapper is allowed but not required — keeping the check in the route is fine.)

---

## Templates

- **Modify:** `templates/login.html`
  - Change form `action` from the hardcoded `/login` to `{{ url_for('login') }}`
  - Replace the `{% if error %}` block with the standard Jinja `get_flashed_messages()` loop so the route can use `flash()` (matches the pattern from Step 2's register page)
  - All links continue to use `url_for()`

- **Modify:** `templates/base.html`
  - The "Sign in / Get started" nav links should swap to a "Logout" link when `session["user_id"]` is present. Use `{% if session.user_id %}` directly in the template — no context processor needed.

No new templates.

---

## Files to change

- `app.py`
  - Add `methods=["GET", "POST"]` to the `/login` route; on POST, call `get_user_by_email()`, verify with `check_password_hash()`, set `session["user_id"]` and `session["user_name"]`, redirect to `/profile`. On failure, `flash()` an error and re-render `login.html`.
  - Replace the `/logout` stub with a real handler that calls `session.clear()` and redirects to `/login` with a success flash.
  - Import `check_password_hash` from `werkzeug.security` (or import the helper indirectly through `database/db.py` if a wrapper is added).
- `templates/login.html` — switch to `url_for()`, render flashed messages.
- `templates/base.html` — conditional nav based on `session["user_id"]`.

---

## Files to create

None.

---

## New dependencies

No new pip packages. `werkzeug.security.check_password_hash` ships with Flask's existing dependency on Werkzeug.

---

## Rules for implementation

- No SQLAlchemy or ORMs
- Parameterised queries only — never f-strings or string concatenation in SQL
- Passwords verified with `werkzeug.security.check_password_hash` — never compare hashes by hand or store plain text
- Use CSS variables — never hardcode hex values in templates or stylesheets
- All templates extend `base.html`
- DB logic stays in `database/db.py` — the route calls `get_user_by_email()`, never runs queries directly
- Use `flash()` for user-facing errors (bad email, bad password, empty fields); use `abort()` for HTTP errors
- Show a single generic error ("Invalid email or password") for both unknown email and wrong password — never reveal which field was wrong
- `session["user_id"]` is the source of truth for "logged in"; do not store the password or hash in the session
- `/logout` must accept the request even when no session exists — clearing an empty session is a no-op, never a 500
- All internal links use `url_for()` — never hardcode `/login`, `/logout`, or `/profile`
- `/profile` is still a Step 4 stub; redirecting to it from `/login` is correct — do not implement the profile page in this step

---

## Definition of done

- [ ] `GET /login` still renders the form and now displays any flashed messages
- [ ] Submitting valid email + password sets `session["user_id"]` and redirects to `/profile`
- [ ] Submitting an unknown email shows an inline "Invalid email or password" error and does not set the session
- [ ] Submitting a wrong password for a real user shows the same generic error
- [ ] Submitting blank email or password shows an inline error and does not crash
- [ ] `GET /logout` clears the session and redirects to `/login`; visiting `/logout` while already logged out does not 500
- [ ] After logging in, the navbar shows a "Logout" link instead of "Sign in / Get started"
- [ ] After logging out, the navbar reverts to "Sign in / Get started"
- [ ] No raw SQL strings are constructed with f-strings or `%` formatting
- [ ] No new query logic in `app.py` — credential lookup goes through `database/db.py`
- [ ] App starts and all existing routes (landing, register, terms, privacy) still work