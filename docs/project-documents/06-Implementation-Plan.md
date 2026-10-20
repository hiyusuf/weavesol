# Implementation Plan — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** what gets built, in what order, and when each part counts as done.

Status key: ✅ done · 🔄 in progress · ⏳ to do · ⛔ blocked

**No code is written until Phase 0 is finished and this plan is approved.**

---

## Decisions

| # | Decision | Status |
|---|----------|--------|
| D1 | Plain PHP 8.2 + MySQL 8, no framework, no Composer | ✅ Decided by owner 2026-10-20 |
| D2 | Runs on an online server **and** on this PC. **To decide:** which copy holds the live books? Recommended: the online server is live; the PC copy is for development, testing and an offline copy restored from the daily backup. Two-way sync is out of scope for version 1 | ⏳ Owner to confirm |
| D3 | Manufacturing and Retail CRM + SMS are in version 1 | ✅ Decided |
| D4 | GST-compliant; VAT / CST dropped | ✅ Decided |
| D5 | No old data to import; CSV master import only | ✅ Decided |
| D6 | Project folder `E:\Yusuf\php\WeaveSol` | ✅ Decided |
| D7 | Online host (shared hosting or VPS) and SMS gateway provider | ⏳ Owner to choose |
| D8 | E-invoice / e-way bill: JSON download in version 1, API later | ⏳ Owner + CA to confirm (depends on turnover) |
| D9 | GST rules for the CA to confirm: which bill charges are taxable, the value-slab basis (per-piece taxable value after discount and charges), karigar job work (registered / unregistered, reverse charge), HSN codes and rates | ⏳ CA, before Phase 5 |
| D10 | Backup direction between the PC copy and the online server (depends on D2) | ⏳ After D2 |

## Phase overview

| # | Phase | Depends on | Status |
|---|-------|-----------|--------|
| 0 | SilkSol screen walkthrough and form specs | — | ⛔ PC screen was locked on 2026-10-20 |
| 1 | Foundation (core, security base, layout, keyboard framework, **print framework**) | 0 | ⏳ |
| 2 | Database schema + reference data | 0 | ⏳ |
| 3 | First-run setup, sign-in, company, year, users, rights | 1, 2 | ⏳ |
| 4 | Masters (incl. CRM customer / group / family and SMS templates) | 3 | ⏳ |
| 5 | Document engine, numbering, charges, GST engine, posting, stock | 4 | ⏳ |
| 6 | Purchase (+ its document prints) | 5 | ⏳ |
| 7 | Sales, orders, packing slips, approval, retail cash memo (+ prints) | 5 | ⏳ |
| 8 | Accounts vouchers, cheques, bank reco, TDS (+ prints) | 5 | ⏳ |
| 9 | Job work, karigar and party job (+ prints) | 5 | ⏳ |
| 10 | Manufacturing (+ prints) | 5 | ⏳ |
| 11 | Stock, labels, barcodes, catalogue, inter-company transfer | 6 | ⏳ |
| 12 | SMS sending, queue, greetings, SMS log | 4, 7 | ⏳ |
| 13 | Reports (registers, books, stock, job, statements, stationery, graphs) | 6–12 | ⏳ |
| 14 | GST returns, e-invoice and e-way bill JSON, IRN entry, period filing | 6, 7, 8, 9 | ⏳ |
| 15 | Utilities: backup, restore, re-post, import, audit log, year-end | 6–11 | ⏳ |
| 16 | Parity test against SilkSol, CA review, install on both machines, go-live | all | ⏳ |

Phase 0 must be finished before any phase starts, including Phase 1 (one rule, no exceptions).
Each module phase builds its own document prints (invoice copies, purja, challans, vouchers…) on
the Phase 1 print framework, so its forms can reach "Parity OK". Phase 13 is for the reports and
registers that span modules.

---

## Phase 0 — SilkSol walkthrough ⛔
Run SilkSol (demo company, 2010-11) the way a user does, and record each screen.
- For every menu item, sub-menu, button that opens a window, right-click menu and shortcut key:
  one file `docs/forms/F-xxx-<name>.md` from `docs/forms/_TEMPLATE.md`, plus a screenshot in
  `docs/forms/screens/`.
- Record: window caption, field list in Enter order, labels, field type / length / format, default
  value, required, look-up source, validation messages, grid columns, totals, buttons and keys,
  what Save does (stock, ledger), and the print options with one sample print (to PDF) per report.
- Record every Settings / Configuration screen with all options and their default values.
- Record the full menu tree in exact order; update `03-App-Flow-Document.md` §2.
- Find screens that open only from a button, key or double-click inside another screen
  (the "hidden" forms) by trying every button and the common keys (F1–F12, Ctrl/Alt
  combinations, double-click, right-click) on every screen.
- Not allowed: decompiling or resource-extracting `SilkSol.exe`, opening `Company.mdb`, copying `.rpt` files.
- Also record the keyboard: every SilkSol key, then test each in the installed-app browser
  window, and write `docs/forms/KEYMAP.md` (SilkSol key → WeaveSol key).
- **Done when:** every SilkSol row (not the "New" rows) in `docs/forms/FORM-INVENTORY.md` is
  "Captured" or "Not in SilkSol", every "New" row has a spec written from the PRD, `KEYMAP.md`
  exists, and the owner has reviewed the list for missing screens.
- **Needs:** the PC unlocked and left on, with SilkSol in its main window, while the walkthrough runs.

## Phase 1 — Foundation ⏳
- Front controller, bootstrap, `.env` loader, config, router with middleware, Request / Response,
  View with `e()`, PDO database with nested transactions, session (secure cookies), CSRF,
  validator, Money and Qty helpers, error handler, logger with redaction, rate limiter, paginator.
- App layout: title bar, menu bar from `config/menu.php`, window manager, status bar.
- Keyboard framework (`entry-form.js`, `line-grid.js`, `lookup.js`) with a demo form.
- Print framework: print layout, `@page` sizes, company header, copies, PDF writer, amount in words.
- Security headers (ARCHITECTURE row 20), deny-all `.htaccess` outside `public/`, CLI-only guard,
  install self-check; test runner with the test-database guard.
- **Done when:** demo form passes the keyboard checklist (Design Brief §6); tests pass.

## Phase 2 — Database ⏳
- `database/schema.sql` for every table in `05-Backend-Schema.md`; `seed.sql` with states and GST
  state codes, units / UQC, starter account groups and system ledgers, GST ledgers, document types,
  default bill charges and postings, starter roles and permissions, common HSN codes for sarees and
  fabrics (rates checked with the CA), first Admin user (forced password change).
- `migrate.php --fresh --demo` with clearly fake demo data.
- **Done when:** schema imports cleanly in phpMyAdmin on MySQL 8.0 and 8.4; integrity tests pass.

## Phase 3 — Sign-in, company, year, users, rights ⏳
- First-run setup screen (one-time token, super-admin with two-factor).
- Sign-in with throttling, two-factor (TOTP) with recovery codes, forced password change,
  sign-out, time-out warning, re-sign-in without losing a typed form.
- Company create / select / edit; financial year create / select; lock date.
- Users, roles, rights matrix (menu × action), per-company role.
- **Done when:** a user without a right can't reach the screen or the URL (403 / 404 tests).

## Phase 4 — Masters ⏳
All Masters-menu forms, matching their `F-xxx` specs. Simple look-up masters (unit, fabric, colour,
pattern, quality, category, city, state, designation, department, holiday, narration) share one
generic screen driven by `config/masters.php`. Ledger, item, karigar, agent, salesman, transporter,
bill charges, series, cheque layout, HSN and reminder letter get their own screens. The CRM
customer, customer group, family and address-book screens and the SMS templates are built here
too, because the cash memo (Phase 7) needs them.
- **Done when:** each master passes its parity checklist and its delete-when-used rule.

## Phase 5 — Document engine ⏳
- `DocumentService` (header, lines, links, validation, save, edit, cancel), document-type
  definitions, `NumberSeriesService`, `ChargeService` (9 slots, %, amount, per qty, add / less,
  taxable flag), `GstService` (place of supply, CGST / SGST / IGST, HSN slabs, rounding),
  `PostingService` (ledger entries, balanced), `StockService` (pieces, movements, locations),
  `OutstandingService` (bill refs and settlements).
- Shared entry-window UI for documents (header, grid, charges, GST panel, totals, buttons).
- **Done when:** unit tests cover GST maths (intra / inter state, taxable charges, discounts,
  rounding, slabs), posting balance, stock moves and numbering under concurrency.

## Phases 6–12 — Modules ⏳
Each module builds its forms from the `F-xxx` specs on top of the Phase 5 engine:
- **6 Purchase:** purja, purchase invoice, return / debit note, fabric purchase, supplier order, purja payment.
- **7 Sales:** order, packing slip, sale invoice (all copies), cash memo, approval issue / sale /
  return, credit note, debit note, proforma, despatch without order, gate pass, transport details.
- **8 Accounts:** receipt, payment, journal, contra, petty cash, bill-wise settlement, cheques,
  supplier payment, TDS, bank reconciliation.
- **9 Job work:** job issue, receive, karigar bill, payment, payment return; party job challan / receive / invoice.
- **10 Manufacturing:** raw material, category, department, formula, costing sheet, raw material
  purchase, material issue, production receipt.
- **11 Stock:** opening stock, adjustment, transfer, labels (5 sizes), barcode labels, catalogue, price list.
- **11 also:** inter-company stock transfer (`TRANSFER_OUT` / `TRANSFER_IN`) and the central item master option.
- **12 SMS:** SMS gateway driver, queue, send SMS (single / group), sale and due SMS, daily
  birthday / anniversary greetings, SMS log, delivery-report webhook.
- **Each done when:** every form in the module is "Parity OK" in the form inventory, and its
  ledger and stock effects match SilkSol on the same test entries (tax by GST rules).

## Phase 13 — Reports ⏳
- Report framework (filters, table, totals, keyboard drill-down, print, PDF, CSV / Excel).
- The remaining `R-xxx` formats (document prints were done in their module phases), in this
  order: ledger, cash book, day book, outstanding → stock → orders and sales → job → trial
  balance, P&L, balance sheet → stationery (labels, envelopes, address book) → graphs.
- **Done when:** each format's printout matches its SilkSol sample in content and layout.

## Phase 14 — GST returns ⏳
GSTR-1 (all tables incl. HSN summary and documents issued), GSTR-3B summary, purchase register
with input tax, e-invoice JSON and e-way bill JSON in the government schema, IRN / EWB storage
and printing of the QR on the invoice, "Mark period filed" (locks edit / cancel for that month).
- **Done when:** the CA signs off on a month of test data.

## Phase 15 — Utilities and year-end ⏳
Backup (download + scheduled local), restore (Admin only, with confirmation), data check /
rebuild balances, re-post documents, CSV master import, audit log viewer, year-end carry-forward.

## Phase 16 — Parity test and go-live ⏳
- Enter the same month of sample business in SilkSol and WeaveSol; compare every report.
- Timed keyboard test with an operator.
- Security checklist (see `docs/ARCHITECTURE.md`). There is no Composer, so instead of
  `composer audit`, check the server's PHP and MySQL versions against their security advisories.
- Install on the local PC and on the online server (`docs/INSTALL.md`).
- **Done when:** the owner signs off.

## Working rules for every phase
- Work on a branch; review before merging into `main`.
- `php tests/run.php` must pass before merging.
- A change of 10 or more files gets the five reviews (planning, security, UI, UX, code).
- A form is built only from its `docs/forms/F-xxx` spec. If the spec is missing or unclear, the
  form waits; nobody guesses.
- Any schema change updates `database/schema.sql`, gives the `ALTER TABLE` SQL, and updates
  `05-Backend-Schema.md`.
