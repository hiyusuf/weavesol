# Technical Requirement Document (TRD) — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** the tools WeaveSol is built on and the rules for using them, so nobody is
> guessing halfway through.

---

## 1. Technology stack

| Layer | Choice | Version | Notes |
|-------|--------|---------|-------|
| Language | PHP | **8.2+** | `declare(strict_types=1)` in every file. Local: XAMPP PHP 8.2.12 (`C:/xampp/php/php.exe`) |
| Database | MySQL | **8.0+** | InnoDB, `utf8mb4_unicode_ci`, CHECK constraints, foreign keys. Local: MySQL Server 8.4 (service `MySQL84`) |
| Web server | Apache (`.htaccess`) or Nginx | — | Document root `public/`; the root `.htaccess` routes into `public/` when the host does not allow changing it |
| Front end | Plain HTML + CSS + vanilla JavaScript | — | No jQuery, no build step |
| Icons | Inline SVG sprite (`public/assets/icons.svg`) | — | No icon font, no CDN |
| Fonts | System font stack | — | Must work offline on the local PC |
| Printing | HTML print views + print CSS (`@page`) | — | A4, A5 / half page, pre-printed stationery, labels, cheques |
| PDF | In-house PDF writer (`app/Services/Pdf/`) | — | Same approach as Total Assure; Unicode font embedded for the ₹ sign |
| Barcode | In-house Code 128 / EAN-13 SVG generator | — | `app/Services/Barcode/` |
| Excel export | CSV + SpreadsheetML (`.xls` XML) | — | The local PHP has no `zip` extension, so true `.xlsx` is not used |
| Amount in words | In-house (Indian lakh / crore system) | — | No `intl` extension locally |
| SMS | Driver interface: `log` (default), HTTP gateway driver | — | DLT template IDs required |
| E-mail | In-house SMTP client, `log` driver for local | — | |
| Tests | In-house test runner | — | `php tests/run.php`, separate test database |

### Hard rules (decided, do not change without a written decision)

- **No framework, no Composer, no Node, no build step.** The code deploys by copying files.
- **No ORM.** PDO with native prepared statements only.
- **No inline JavaScript and no inline event handlers.** A strict Content Security Policy allows
  `script-src 'self'`. Every page script is its own file.
- **Secrets only in `.env`** (git-ignored), never in the database or the code.
- **Installs use `database/schema.sql` + `database/seed.sql`**, importable through phpMyAdmin.
  A structure change means editing `schema.sql` and supplying the matching `ALTER TABLE` SQL.
  No versioned migration files.
- **Works offline on the local PC:** no CDN fonts, scripts or styles.
- **No SilkSol code or files:** nothing is extracted from `SilkSol.exe`, and its `.rpt` files and
  `Company.mdb` are not used. See `AGENTS.md`.

## 2. Required PHP extensions

`pdo_mysql`, `mbstring`, `openssl`, `curl`, `json`, `fileinfo`, `gd` (photos, labels), `zlib` (PDF
compression), `bcmath` (exact decimal maths for money and GST). All are present in the local XAMPP.
The online server must provide the same set.

## 3. Application architecture

```
Browser ──► public/index.php ──► app/bootstrap.php
                                   │
                                   ├─ Core/Router  ── routes/web.php (route + middleware list)
                                   ├─ Middleware   ── auth · company+year context · perm:menu.action · CSRF (every POST)
                                   ├─ Controllers  ── HTTP only: read input → call a service → render a view / JSON
                                   ├─ Services     ── ALL business logic (documents, posting, stock, GST, numbering, reports)
                                   ├─ Repositories ── SQL for one area; every query takes company_id (+ fy_id)
                                   ├─ Core/Database (PDO, nested transactions, row locks)
                                   └─ views/       ── plain PHP templates, every value escaped with e()
```

| Folder | Contents |
|--------|----------|
| `app/Core` | App, Router, Request, Response, Database, Session, Csrf, Auth, Totp, Validator, Money (integer paise), Qty (milli-units), Paginator, RateLimiter, Logger, ErrorHandler, Env, View, Context (company + year) |
| `app/Middleware` | auth, guest, context (company and year chosen), permission, CSRF, forced password change, two-factor required |
| `app/Controllers` | `Auth/`, `Company/`, `Masters/`, `Purchase/`, `Sales/`, `Job/`, `Manufacturing/`, `Accounts/`, `Stock/`, `Crm/`, `Reports/`, `Gst/`, `Utilities/`, `Admin/` |
| `app/Services` | DocumentService, PostingService, StockService, GstService, NumberSeriesService, ChargeService, OutstandingService, YearEndService, LabelService, ChequeService, SmsService, ReportService, ExportService, PrintService, AuditService, PermissionService, BackupService, `Pdf/*`, `Barcode/*`, `Sms/*`, `Mail/*` |
| `app/Documents` | One definition class per document type (fields, line columns, charges, stock effect, posting rule, print formats) |
| `app/Reports` | One class per report (filters, query, columns, totals, print layout) |
| `app/Exceptions` | Validation, BusinessRule, Forbidden, NotFound, PeriodLocked, StockShortage |
| `config/` | `app.php` (reads `.env`), `menu.php` (menu tree = SilkSol menu order), `masters.php` (simple look-up masters handled by one generic screen) |
| `database/` | `schema.sql`, `seed.sql`, `migrate.php`, `seed.php`, `seeds/` |
| `views/` | `layouts/` (app, print, auth), `partials/`, one folder per module, `print/` (all print formats), `errors/` |
| `public/` | `index.php` (only entry point), `router.php` (dev server), `assets/` (css, js, icons, fonts, images) |
| `storage/` | logs, sessions, uploads (photos, logos), backups, exports, sms and mail logs, cache. Not web-accessible |
| `cron/` | `daily.php` (birthday / anniversary SMS, due reminders, local backup), `process-queue.php` (send SMS / e-mail) |
| `tests/` | `run.php` + test classes |
| `docs/` | project documents, `forms/` (one spec per screen), `silksol-reference/` |

### JavaScript files

| File | Used on |
|------|---------|
| `public/assets/js/app.js` | Shared: menu bar, keyboard shortcuts, dialogs, toasts |
| `public/assets/js/entry-form.js` | Every entry screen: Enter-to-next field, required fields, F-key look-ups, save / cancel keys |
| `public/assets/js/line-grid.js` | Item-line grids (invoice, purja, issue, receive): add / delete row, totals, barcode scan |
| `public/assets/js/lookup.js` | Pop-up search lists (party, item, karigar, agent…) with type-ahead |
| `public/assets/js/report-view.js` | Report filters, column sort, export, print |

## 4. Configuration (`.env`)

| Key | Purpose |
|-----|---------|
| `APP_NAME`, `APP_ENV` (`local` / `production`), `APP_DEBUG`, `APP_URL`, `APP_TIMEZONE` (`Asia/Kolkata`), `APP_KEY` | App basics |
| `DB_HOST`, `DB_PORT`, `DB_DATABASE`, `DB_USERNAME`, `DB_PASSWORD`, `DB_TEST_DATABASE` | Database |
| `SESSION_NAME`, `SESSION_LIFETIME_MINUTES`, `SESSION_SECURE_COOKIE` | Session |
| `MAIL_*` | E-mail (`log` driver locally) |
| `SMS_DRIVER`, `SMS_API_URL`, `SMS_API_KEY`, `SMS_SENDER_ID`, `SMS_DLT_ENTITY_ID` | SMS gateway |
| `BACKUP_PATH`, `BACKUP_KEEP_DAYS` | Local backup folder and retention |
| `UPLOAD_MAX_MB` | Photo and logo upload limit |

Business settings live in the database per company, not in `.env`: number series in
`number_series`, bill charges and their posting ledgers in `bill_charges` / `bill_charge_postings`,
print formats in `print_formats`, and GST options, print options and the SilkSol "configuration"
switches in `settings`. They are edited from the Masters menu and **Utilities › Settings**.

## 5. Integrations

| Integration | Flow | Security |
|-------------|------|----------|
| SMS gateway | Queue → `cron/process-queue.php` → HTTP API | Key in `.env`; DLT template ID per message; numbers masked in logs |
| SMTP | Queue → cron → SMTP | Credentials in `.env` |
| GST e-invoice / e-way bill | Version 1: JSON file download for portal upload, then type in the IRN / EWB number | JSON built from posted data only |
| Bank cheque printing | HTML print view positioned in millimetres per bank layout | — |

## 6. Non-functional requirements

| Area | Requirement |
|------|-------------|
| **Security** | Full list in `docs/ARCHITECTURE.md` (Security controls, rows 1–30), including security headers, restore rules and first-run setup. In short: prepared statements; `e()` on all output; CSRF token on every POST; HttpOnly + SameSite=Lax + Secure cookies, session regenerated on login, inactivity timeout; per-account and per-IP sign-in throttling; bcrypt / argon2id passwords; TOTP two-factor for admin and high-risk rights; permissions checked on the server for every route; every record fetched by ID is checked against the user's company (404 if out of scope); uploads checked by size, `finfo` MIME, extension and real image content, saved under random names in `storage/`; secrets redacted in logs; CSV formula injection neutralised |
| **Money** | `DECIMAL(15,2)` columns; integer-paise maths (`Money`) with half-up rounding; GST rounded per line, invoice total rounded to the rupee with a round-off line |
| **Quantity** | `DECIMAL(14,3)`; display decimals per item type / unit (SilkSol `STYL` format, e.g. `#######0` for pieces, `####0.00` for metres) |
| **Concurrency** | Number series and stock pieces are locked with `SELECT … FOR UPDATE`; a document and its postings and stock movements are saved in one transaction |
| **Integrity** | Ledger postings and stock movements are written only by `PostingService` / `StockService`; editing a document replaces its postings atomically; the audit log keeps before / after values |
| **Errors** | `APP_DEBUG=false` in production: generic error pages; details only in `storage/logs/` |
| **Performance** | Saving an invoice < 1 s; look-up pop-ups < 200 ms with 50,000 items; every list is paginated; reports over a full year < 5 s on shared hosting |
| **Keyboard** | Every entry screen is usable without a mouse (see `04-Design-Brief.md` §6) |
| **Compatibility** | Latest Chrome and Edge (main), Firefox; entry screens designed for 1366 × 768 and larger; reports and look-ups usable down to 360 px |
| **Hosting** | Must run on shared hosting without SSH (phpMyAdmin import, panel cron) and on local XAMPP |
| **Backups** | In-app SQL backup download; daily local backup on the PC; documented server backup |

## 7. Environments

| Environment | Details |
|-------------|---------|
| Local | XAMPP PHP 8.2 + MySQL 8.4 on this PC. DB `weavesol`, test DB `weavesol_test`. Dev server `php -S localhost:8810 -t public public/router.php` (the `weavesol` entry in `.claude/launch.json`). Can also be used as the shop's offline copy |
| Production | Online server (host to be chosen). `APP_ENV=production`, `APP_DEBUG=false`, HTTPS only |

Both run the same code. Which copy holds the live books, and how the two are kept in step, is an open
decision (see `06-Implementation-Plan.md`, decision D2).

## 8. Commands (once code exists)

```bash
php database/migrate.php --fresh --demo   # rebuild local DB: schema + reference data + demo data
php database/migrate.php --seed           # (re)apply reference data (idempotent)
php tests/run.php                         # full test suite on the throw-away test DB
php cron/daily.php                        # daily job
php cron/process-queue.php                # send queued SMS / e-mail
```

## 9. Source control

- Git, branch `main`. Remote to be created.
- Work on a branch, merge into `main` when reviewed.
- Never commit `.env`, backups, uploads or copies of SilkSol files.
