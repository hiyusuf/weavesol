# Architecture, Business Rules & Security — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

The rules in this file are binding. Other documents link here instead of repeating them.

## Layers

```
routes/web.php ─► Middleware (auth · context · perm · CSRF)
              ─► Controller (read input, call one service, render)
              ─► Service (business rules, transactions)
              ─► Repository (SQL, always scoped by company_id / fy_id)
              ─► MySQL
views/ (plain PHP, every value through e())
```

- Controllers never hold business logic. Services never read `$_GET`, `$_POST` or `$_SESSION`;
  they get a `Context` (user, company, year) and plain arrays.
- Each document type is a definition class in `app/Documents/` describing: header fields, line
  columns, which bill charges apply, stock effect, posting rule, number series, print formats.
  The engine (`DocumentService`) is shared, so adding a document type is mostly a definition.
- Each report is a class in `app/Reports/` (filters, query, columns, totals, print view).

## Company and year context

- After sign-in the user picks a company and a financial year. Both are stored in the session and
  passed to every service as `Context`.
- Every repository query has `company_id = :company` (and `fy_id = :fy` on year tables). There is
  no "all companies" query outside Admin utilities.
- A record ID from another company or year returns **404**, never the record.

## Document engine

Save flow (one database transaction):
1. Validate the header and lines against the document definition and the user's rights.
2. Check the lock date; check credit limit / hold (warn or block per setting); check stock.
3. Take the number from `number_series` (`SELECT … FOR UPDATE`); new documents only.
4. Work out amounts: line amount → discount → bill charges → taxable value → GST → round-off → net.
5. Write the document, lines, charges, taxes, transport and links.
6. `StockService` writes stock movements and moves stock pieces.
7. `PostingService` writes ledger entries; their debits and credits must be equal.
8. `OutstandingService` writes bill refs / settlements.
9. `AuditService` logs before / after.
10. Commit. Print and SMS happen after the commit.

Edit = the same flow, which first removes the document's old movements, entries and refs inside the
same transaction. Edit is refused once the document has an IRN / e-way bill or its GST period is
marked filed.

Cancel = status `CANCELLED`; its stock movements, ledger entries and bill refs are removed in one
transaction; the number stays. **No numbered document is ever deleted** (only unnumbered drafts).
Cancel is refused while settlements exist against the bill, after the period is filed (use a
credit / debit note), and when an IRN exists unless its portal cancellation (within 24 hours) has
been recorded. Full rules: `05-Backend-Schema.md` §5.

## Amounts and GST (`GstService`)

- Money is calculated in integer paise (`Money`); quantities in thousandths (`Qty`). No floats.
  qty × rate uses `bcmath` at scale 7 and is rounded half-up to paise once.
- Line amount = qty × rate. Trade discount (% or amount) comes off the line.
- Bill charges (9 slots, `ChargeService`): % of gross, fixed amount or per quantity; add or less.
  A charge marked **taxable** is shared across lines in proportion to their value and becomes part
  of the taxable value. Leftover paise from the split go to the line with the largest value.
- Under GST, packing, freight and other amounts the seller charges on the invoice are part of the
  value (section 15), so an **add** charge defaults to taxable. Marking an add charge non-taxable
  needs a reason in the charge master (e.g. a pure reimbursement); the CA confirms the list in
  Phase 2. **Less** charges given on the invoice (trade discount, commission deducted) reduce the
  taxable value.
- Agent commission is a charge only if the business deducts it on the bill (SilkSol Commission
  slots); otherwise it is an agent-commission report, not a bill line.
- GST rate per line from the item's HSN, using value slabs where they exist. The slab is chosen on
  the per-piece taxable value (after discount and the line's share of taxable charges), so a
  shared charge that pushes a piece over the limit changes its rate. The CA confirms this in Phase 2.
- Place of supply: the ship-to state when a ship-to party is given, else the party's state; a
  counter cash memo is always the company's own state. Same as the company state → CGST + SGST
  (half each); different → IGST. Unregistered or consumer buyers → B2C.
- Tax is rounded per line to the paisa; the invoice net is rounded to the rupee with a round-off
  ledger line (setting), on the debit or credit side as needed.
- Credit and debit notes refer to the original invoice number and date.
- Amount in words uses the Indian system (lakh, crore).

## Posting rules (`PostingService`)

Each document type posts like SilkSol does, plus GST ledgers. The ledger used for each part comes
from `bill_charge_postings` and the company's system ledgers:

| Document | Debit | Credit |
|----------|-------|--------|
| Sale invoice / cash memo | Party (or Cash / Bank for a cash memo split); round-off if negative | Sales; Output CGST / SGST / IGST; added charges; round-off if positive |
| Credit note | Sales return; output GST | Party |
| Purja / purchase | Purchase; Input CGST / SGST / IGST; added charges | Supplier |
| Purchase return / debit note | Supplier | Purchase return; input GST |
| Karigar bill | Job / work expense (job ledger); input GST if the karigar is registered | Karigar; TDS payable when TDS is deducted |
| Receipt | Cash / Bank | Party (bill-wise settlement) |
| Payment | Party / expense | Cash / Bank |
| Journal / contra | As entered | As entered |
| Production receipt / material issue | Stock / consumption per setting | Stock / consumption per setting |

"Less" charges (discount, commission deducted) post to their own ledgers on the opposite side.
Every posting rule has a test that checks the debits equal the credits and the ledgers are right.

## Stock rules (`StockService`)

- Stock is held by item **and** by piece (`stock_pieces`, numbered for labels and barcodes) where
  the item type uses pieces; than / metre items can be held by quantity only.
- Locations: in hand, with a karigar, on approval with a party, sold, returned, consumed.
- Job issue moves stock to the karigar; receive brings it back (the item can change stage per its
  job route). Approval issue moves stock to the party; sale or return clears it.
- Negative stock is blocked unless the setting allows it.
- `stock_balances` is a summary rebuilt from `stock_movements` by **Utilities › Check data**.

## Numbering
- Gap-free per company, document type, series and year. The number is given when the document is
  first posted; drafts have none. A cancelled number is kept.
- GST limit: at most 16 characters including the prefix, only `A–Z a–z 0–9 / -`. Each GST series
  needs its own prefix, so one number is never used twice under the GSTIN in a year (also a
  unique key).

## Year-end
- New year: copies masters' year settings and series (next numbers reset to 1).
- Carry forward: closing balances of **balance-sheet ledgers only** become openings. Trading and
  P&L ledgers start at zero, the year's net profit / loss goes to the capital ledger (or the
  ledger chosen in Settings), and the closing stock value becomes the opening stock. Also carried
  forward: unsettled bill refs as opening bills, stock pieces and balances by location, open
  orders and approvals, and karigar stock.
- A preview is shown first, and a result summary is kept.
- Can be re-run until the old year is closed; closing locks the old year.

## Security controls (checked on every major change)

| # | Control | How WeaveSol meets it |
|---|---------|-----------------------|
| 1 | SQL injection | PDO prepared statements only; sort / filter columns from an allow-list |
| 2 | XSS | `e()` on every output; no raw HTML from data; strict CSP, no inline scripts |
| 3 | CSRF | Token on every POST, checked by middleware; SameSite=Lax cookies |
| 4 | File uploads | Photos and logos only: size limit, `finfo` MIME allow-list, extension match, re-encoded with GD, random names under `storage/` (outside the web root), served after a rights check with `nosniff` |
| 5 | SSRF | The only outbound calls are the SMS / mail gateways configured in `.env`; no URL comes from user input |
| 6 | Object authorization | Every fetch by ID is checked against the user's company (and year); 404 if out of scope |
| 7 | Rate limiting | Sign-in, two-factor and password reset throttled per account and per IP |
| 8 | Passwords | `password_hash` (bcrypt / argon2id); never logged; forced change on first sign-in |
| 9 | Two-factor | TOTP offered to all users; **required** for super-admins, Admins, and any role with delete, cancel, year-end, lock-date, user-admin, backup or restore rights; a fresh code is asked again before backup, restore and year-end. A code already used in the same time step is refused. Secrets are encrypted with AES-256-GCM (openssl) using a key derived from `APP_KEY`; changing `APP_KEY` means re-enrolling two-factor (documented). 10 one-time recovery codes, stored hashed |
| 10 | Server-side permissions | `perm:` middleware on every route + checks inside services; hidden menu items are not the control |
| 11 | Row-level security | MySQL has none; enforced by company / year scoping in repositories, covered by tests |
| 12 | JWT | Not used (server sessions) — not applicable |
| 13 | API secrets | SMS / mail keys only in `.env` on the server; never sent to the browser |
| 14 | Tokens in local storage | None; session in an HttpOnly, Secure, SameSite cookie |
| 15 | CORS | No cross-origin API; no CORS headers sent (same-origin only) |
| 16 | Webhooks | SMS delivery reports, if used, go to a CSRF-exempt route that checks `SMS_WEBHOOK_SECRET` with `hash_equals` (or the provider's signature), only updates a `message_log` row matched by the provider's message ID, and ignores repeats |
| 17 | Source maps | None (no build step) |
| 18 | Sensitive data in logs | Logger redacts passwords, tokens, OTPs, PAN, bank numbers, phone numbers; production error pages are generic |
| 19 | Dependencies | No Composer / npm packages; keep PHP and MySQL patched on both machines and check their advisories at each release |
| 20 | Security headers | CSP `default-src 'self'; script-src 'self'; object-src 'none'; base-uri 'self'; form-action 'self'; frame-ancestors 'none'`; `X-Frame-Options: DENY`; `X-Content-Type-Options: nosniff`; `Referrer-Policy: same-origin`; HSTS on HTTPS in production |
| 21 | Files outside `public/` | If the host can't point the document root at `public/`, every other folder (`app`, `config`, `cron`, `database`, `storage`, `tests`, `views`, `docs`) gets a deny-all `.htaccess`, and dotfiles (`.env`) are blocked. Scripts in `cron/`, `database/` and `tests/` stop unless `PHP_SAPI === 'cli'`. An install self-check refuses to start if `/.env` or `/storage/` can be fetched over HTTP. On Nginx the same rules go in the server block (`docs/INSTALL.md`) |
| 22 | First admin | No user in `seed.sql`. A first-run setup screen works only while the `users` table is empty and needs the one-time token printed in `storage/setup-token.txt`; it creates the super-admin with password and two-factor, then deletes the token |
| 23 | Backup | Super-admin only, fresh two-factor, logged; the file is signed with HMAC(`APP_KEY`) |
| 24 | Restore | Super-admin only, fresh two-factor; refuses a file without a valid WeaveSol signature; refuses statements outside plain `CREATE TABLE` / `INSERT` / `DROP TABLE` for WeaveSol tables (no `INTO OUTFILE`, `LOAD DATA`, `GRANT`, `CREATE USER`, `DEFINER`, routines, triggers, `SET GLOBAL`); `PDO::MYSQL_ATTR_LOCAL_INFILE` off; an automatic backup is made first. The database user has no FILE, GRANT or SUPER rights |
| 25 | Sessions | Regenerated on sign-in, after two-factor and on company switch; company choice checked against `user_companies`; `is_active` re-checked every request; 30-minute idle and 12-hour absolute limit; a password change ends other sessions; sign-out by POST; Secure cookie forced when `APP_ENV=production`; client IP from `REMOTE_ADDR` only |
| 26 | State-changing GETs | None: printing (which counts copies), exports and sign-out are POST or count in a separate POST |
| 27 | CSV / Excel export | Text cells starting with `=`, `+`, `-`, `@`, tab or carriage return are prefixed with `'`; numbers stay numbers |
| 28 | File paths from data | Print views are chosen by key from a fixed list; photo paths are resolved and must sit inside `storage/uploads/<company>/` |
| 29 | Search | `LIKE` look-ups escape `%` and `_`; image size checked before GD decodes an upload |
| 30 | Local network use | When the PC copy is used by other PCs on the shop network, it runs on HTTPS (XAMPP self-signed or local CA), otherwise session cookies travel in clear text |

## Testing
- `php tests/run.php` on a throw-away test database. The runner refuses to start if
  `DB_TEST_DATABASE` is empty or equals `DB_DATABASE`.
- Must cover: GST maths, posting balance per document type, stock moves, numbering under
  concurrency, scope (404) and permission (403) checks, CSRF, sign-in throttling, year-end
  carry-forward, cancel / edit reversals.
- Parity tests: sample entries with the expected ledger and stock effect taken from SilkSol.
