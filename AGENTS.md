# AGENTS.md — WeaveSol

Instructions for any AI coding agent (Claude Code, Codex, Cursor…) working in this repository.
`CLAUDE.md` imports this file.

## What this project is
WeaveSol: saree trade, job-work, manufacturing and accounting software in plain PHP 8.2 + MySQL 8.
It must match the screens, fields, flow, settings and printouts of SilkSol 2.00.1510 (the software
the business uses today), with GST in place of VAT/CST. Read `README.md`, then the documents below.

## Read first
| Need | File |
|------|------|
| What to build | `docs/project-documents/01-Product-Requirement-Document.md` |
| Stack and hard rules | `docs/project-documents/02-Technical-Requirement-Document.md` |
| Screens and flows | `docs/project-documents/03-App-Flow-Document.md` |
| Look and keyboard | `docs/project-documents/04-Design-Brief.md` |
| Tables | `docs/project-documents/05-Backend-Schema.md` |
| Order of work | `docs/project-documents/06-Implementation-Plan.md` |
| Business rules and security | `docs/ARCHITECTURE.md` |
| Each screen | `docs/forms/FORM-INVENTORY.md` and `docs/forms/F-xxx-*.md` |
| Trade words | `docs/GLOSSARY.md` |
| Principles | `.specify/memory/constitution.md` |
| Current state | `handover.md` |

## Current phase
Documentation only. **Do not write application code** until Phase 0 (SilkSol walkthrough) is
finished and the owner approves the plan. Check `handover.md` for the latest status.

## SilkSol: allowed and not allowed
- Allowed: running SilkSol and using it like a normal user (menus, buttons, keys, double-click,
  right-click) on the demo company; screenshots; printing samples to PDF; reading
  `docs/silksol-reference/`.
- **Not allowed:** decompiling, disassembling or resource-extracting `C:\SilkSol\SilkSol.exe` or
  any SilkSol binary; opening or cracking `C:\SilkSol\Company.mdb`; opening, converting or copying
  `.rpt` files; copying SilkSol's logo, name, images or long text into WeaveSol; entering real
  customer data in SilkSol.
- If a screen's behaviour is unclear, capture it again or ask the owner. Don't guess, and don't
  look inside the program.

## Hard rules (from the TRD and constitution)
- Plain PHP 8.2, MySQL 8. **No framework, no Composer, no Node, no build step, no CDN.**
- PDO prepared statements only. No ORM. No string-built SQL.
- Business logic in `app/Services` (and `app/Documents` definitions). Controllers only read input,
  call a service, render. Views escape everything with `e()`.
- Every query is scoped by `company_id` (and `fy_id`). Out-of-scope IDs return 404.
- Every route declares its permission middleware. Every POST has CSRF.
- No inline JavaScript or event handlers (strict CSP). Page scripts are files under `public/assets/js/`.
- Money in integer paise, quantity in thousandths. No floats for money.
- Documents, postings and stock save in one transaction through `DocumentService`. Nothing else
  writes `ledger_entries` or `stock_movements`.
- GST sale documents are cancelled, never deleted.
- Schema changes: edit `database/schema.sql`, give the `ALTER TABLE` SQL, update `05-Backend-Schema.md`.
  No migration files.
- Secrets only in `.env`. Never commit `.env`, backups or uploads.
- Build a screen only from its `docs/forms/F-xxx` spec. If there's no spec, stop and say so.

## Commands (once code exists)
```bash
php database/migrate.php --fresh --demo
php -S localhost:8810 -t public public/router.php
php tests/run.php
```

## Done means
Tests pass, the form's parity checklist is filled in, the docs are updated, and the security
checklist in `docs/ARCHITECTURE.md` still holds.

## Writing style for docs and UI text
Plain, short sentences. Use the business's own words (purja, karigar, angadia, bilty…). Don't put
internal codes or database IDs in front of users.
