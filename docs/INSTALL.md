# Installation & Deployment — WeaveSol

_Version 0.1 (draft) · 2026-10-20 — target procedure; the code does not exist yet._

WeaveSol runs in two places from the same code: the online server and the local PC (XAMPP).

## 1. Requirements
- PHP 8.2+ with `pdo_mysql`, `mbstring`, `openssl`, `curl`, `json`, `fileinfo`, `gd`, `zlib`, `bcmath`.
- MySQL 8.0+ (local PC: MySQL Server 8.4, service `MySQL84`).
- Apache with `mod_rewrite` (or Nginx). HTTPS on the online server.

## 2. Database
1. Create a database `weavesol` (`utf8mb4_unicode_ci`) and a user with rights on it only:
   `SELECT, INSERT, UPDATE, DELETE, CREATE, ALTER, DROP, INDEX, LOCK TABLES, REFERENCES`.
   Never FILE, GRANT, SUPER or PROCESS.
2. Import `database/schema.sql`, then `database/seed.sql` (phpMyAdmin › Import, or the MySQL
   command line).
3. Local only: `php database/migrate.php --fresh --demo` builds the same with demo data.

## 3. Configuration
1. Copy `.env.example` to `.env`.
2. Set `APP_URL`, `APP_KEY` (`php -r "echo bin2hex(random_bytes(32));"`), the `DB_*` values, and
   on the server `APP_ENV=production`, `APP_DEBUG=false`, `SESSION_SECURE_COOKIE=true`.
3. Set the SMS and mail values when the providers are chosen.

## 4. First-run setup
`seed.sql` contains no user. On the first visit, while there are no users, WeaveSol writes a
one-time token to `storage/setup-token.txt` and shows the setup screen. Enter the token, then
create the super-admin (password and two-factor). The token file is deleted automatically. Then
create the company, financial year and users.

Lost two-factor for the only super-admin: run `php cron/reset-2fa.php <username>` on the server
(CLI only; on hosting without SSH, use the host's "run PHP script" or cron-once feature). It is logged.

## 5. File permissions
`storage/` must be writable by the web server. Nothing else needs write access.

## 6. Web server
- Document root = `public/` (preferred). If the host does not allow that, the root `.htaccess`
  routes into `public/`, and every other folder carries a deny-all `.htaccess`; dotfiles are
  blocked. This fallback works only on Apache with `AllowOverride All`. WeaveSol's install
  self-check refuses to run if `/.env` or `/storage/` can be downloaded.
- Nginx: set `root …/public;` and add `location ~ /\. { deny all; }`. Never serve the project root.
- Local dev server: `php -S localhost:8810 -t public public/router.php`.
- Local "shop" use: an Apache virtual host in XAMPP pointing at `E:\Yusuf\php\WeaveSol\public`.
  If other PCs on the shop network use it, turn on HTTPS in XAMPP (self-signed or local CA
  certificate) and set `SESSION_SECURE_COOKIE=true`.

## 7. Scheduled jobs
| Job | Online server | Local PC |
|-----|---------------|----------|
| `php cron/process-queue.php` (SMS / mail) | every minute (panel cron) | Windows Task Scheduler, every 5 minutes |
| `php cron/daily.php` (greetings, due SMS, backup, clean-up) | daily 07:00 | Task Scheduler, daily |

## 8. Backups
- Online: the host's daily backup + **Utilities › Backup** download before year-end and before upgrades.
- Local: `cron/daily.php` writes a dated SQL file to `BACKUP_PATH` and keeps `BACKUP_KEEP_DAYS` days.
- Restore: **Utilities › Restore** (Admin, two-factor) or phpMyAdmin import.

## 9. Upgrading
Back up, copy the new files (keep `.env` and `storage/`), run any `ALTER TABLE` SQL from the
release note in phpMyAdmin, then sign in and check **Utilities › Check data**.
