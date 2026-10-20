# WeaveSol Constitution

## Core Principles

### I. Parity With SilkSol, Built Our Own Way
Every SilkSol screen and printout MUST have a WeaveSol equivalent with the same fields, field order,
defaults, keys, effect and printed information, except where GST requires a change.
SilkSol MUST be studied only by using it. Decompiling or extracting `SilkSol.exe`, opening
`Company.mdb`, and reusing `.rpt` files, logos or text are forbidden.
A screen MUST be built only from its `docs/forms/F-xxx` spec.
Rationale: staff switch without retraining, and the software stays ours.

### II. Dependency-Free Stack
The application MUST be plain PHP 8.2 on MySQL 8, with no framework, Composer, Node, build step or
CDN. Any third-party code requires a written decision in `06-Implementation-Plan.md`.
Rationale: it must deploy by copying files to shared hosting and run offline on the local PC.

### III. Layered Code
Business logic MUST live in `app/Services` and `app/Documents`. Controllers MUST only read input,
call a service and render. Views MUST escape every value with `e()`.

### IV. Correct Books
Every document MUST save with its ledger entries, stock movements and bill refs in one transaction,
through `DocumentService`. Ledger entries per document MUST balance. Money MUST be computed in
integer paise. GST sale documents MUST be cancelled, never deleted, and numbering MUST be gap-free.

### V. Scoped Access
Every route MUST declare its permission. Every query MUST be scoped by company (and year).
An out-of-scope ID MUST return 404.

### VI. Secure by Default
Prepared statements only; CSRF on every POST; strict CSP with no inline JavaScript; secrets only in
`.env`; uploads stored outside the web root and checked; two-factor sign-in for admin and
high-risk rights.

### VII. Schema Changes Through schema.sql
`database/schema.sql` is the only structure source. Each change MUST update it, give `ALTER TABLE`
SQL for phpMyAdmin, and update `05-Backend-Schema.md`. No migration files.

### VIII. Tests Must Pass
`php tests/run.php` MUST pass before merging. GST maths, posting balance, stock moves and scope
checks MUST have tests. A bug fix SHOULD come with a test.

## Constraints
- Runs on an online server (no SSH assumed) and on local XAMPP from the same code.
- GST-compliant; VAT / CST are not supported.

## Development Workflow
- Work on a branch; review before merging into `main`.
- Changes of 10 or more files get planning, security, UI, UX and code reviews.
- A change to a principle, business rule or schema MUST update the matching document in `docs/`.

## Governance
This constitution overrides other practice in the project. Amendments are recorded here and
approved by the owner.

**Version**: 1.0.0 | **Ratified**: 2026-10-20 | **Last Amended**: 2026-10-20
