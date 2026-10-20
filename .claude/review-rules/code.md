# WeaveSol - code review rules

Read by the `review-code` skill. These rules override its defaults. Source of truth:
`.specify/memory/constitution.md` and `docs/ARCHITECTURE.md`.

## Stack
- Plain PHP 8.2 on MySQL 8. No framework, Composer, Node, build step or CDN. Flag any new dependency as a blocker unless the diff documents the decision.
- Code must run on shared hosting without SSH and on local XAMPP. Flag anything that needs a shell or a long-running process at web runtime.

## Layering
- Controllers read input, call a service, render. Flag business logic in `app/Controllers` or `views/`.
- Services don't read `$_GET`, `$_POST` or `$_SESSION`; they take a `Context`.
- Every repository query filters on `company_id` (and `fy_id` for year tables). Flag one that doesn't.

## Documents, money and stock
- Documents save only through `DocumentService`, in one transaction with postings, stock and bill refs. Flag any direct `INSERT`/`UPDATE`/`DELETE` on `ledger_entries`, `stock_movements`, `stock_pieces.location` or `bill_settlements` outside `PostingService` / `StockService` / `OutstandingService`.
- Money in integer paise (`Money`), quantity in thousandths (`Qty`). Flag floats for money or quantity.
- Ledger entries per document must balance; flag a posting rule without a balance test.
- GST sale documents are cancelled, never deleted. Flag a delete path for them.
- Number series taken under `SELECT … FOR UPDATE`. Flag numbering without a lock.

## Parity
- A screen must match its `docs/forms/F-xxx` spec (fields, order, defaults, keys). Flag differences that the spec doesn't explain.
- Flag any code, text, image or layout file that looks extracted from SilkSol (`.rpt`, binary resources, copied captions beyond field labels).

## Schema
- `database/schema.sql` is the only structure source; changes include `ALTER TABLE` SQL and update `05-Backend-Schema.md`. Flag migration files.

## Tests
- `php tests/run.php` must pass. Flag deleted or skipped tests. A bug fix needs a test.

## Docs
- A change to a business rule, flow or schema updates the matching file in `docs/`. Flag it if missing.
