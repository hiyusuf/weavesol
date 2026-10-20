# WeaveSol - security review rules

Read by the `review-security` skill. Source of truth: `docs/ARCHITECTURE.md` (Security controls)
and `.specify/memory/constitution.md` (Principles V and VI).

## Routes and access
- Every route in `routes/web.php` declares `auth` and `perm:` middleware. Flag any route without them, including AJAX look-ups and print / export routes.
- Every record fetched by ID is checked against the session company (and year). Out of scope returns 404. Flag a 403 that leaks existence, or a look-up that lists another company's data.
- Delete, cancel, year-end, lock date, restore and user admin also check rights inside the service and need two-factor. Flag a path that skips this.

## Data access
- PDO prepared statements only. Sort / filter columns from an allow-list. Flag string concatenation into SQL.

## Output
- `e()` on every value in views and print views. Flag raw `echo` of data.
- Strict CSP: no inline `<script>`, no `onclick=` etc. Flag them.
- CSV / Excel exports neutralise formula injection in text cells (`=`, `+`, `-`, `@`, tab, carriage return at the start); numbers stay numbers.

## Requests
- Every POST carries a CSRF token. Flag a POST without it (SMS delivery-report webhooks are verified by provider secret instead).
- Sign-in, two-factor and password reset are throttled per account and per IP.

## Sessions and auth
- Session: strict mode, HttpOnly, SameSite=Lax, Secure on HTTPS, regenerated on sign-in, inactivity timeout.
- Passwords with `password_hash`; never logged. Same response for unknown and known users.

## Uploads
- Photos and logos only: size limit, `finfo` MIME allow-list, extension match, re-encode with GD, random names under `storage/uploads/`, served after a rights check with `nosniff`.

## Restore and backup
- Backup and restore are super-admin only with a fresh two-factor code, and logged. Backups are signed with HMAC(`APP_KEY`); restore refuses an unsigned or changed file and any statement other than plain `CREATE TABLE` / `INSERT` / `DROP TABLE` on WeaveSol tables (no `INTO OUTFILE`, `LOAD DATA`, `GRANT`, `CREATE USER`, `DEFINER`, routines, triggers, `SET GLOBAL`). `LOCAL INFILE` stays off. An automatic backup runs before a restore. Flag anything weaker. (A MySQL transaction does not protect DDL.)

## Install and files
- No user in `seed.sql`; first super-admin only through the one-time-token setup screen. Flag a seeded password or hash.
- Scripts in `cron/`, `database/`, `tests/` exit unless `PHP_SAPI === 'cli'`. Folders outside `public/` carry deny-all `.htaccess`. Flag a web-reachable path to them.
- Security headers per `docs/ARCHITECTURE.md` row 20. Flag a missing header.
- Print views chosen by key from a fixed list; upload paths resolved inside `storage/uploads/<company>/`. Flag `include` of a path from data.
- Audit log drops password hashes and TOTP secrets and masks PAN, bank and phone numbers.

## Outbound calls
- Only the SMS / mail gateways from `.env`. Flag any outbound URL built from user input (SSRF).

## Secrets and logs
- Secrets only in `.env`; never committed. Logs redact passwords, OTPs, tokens, PAN, bank account and phone numbers. Production shows generic error pages.
