# WeaveSol

Saree trade software: wholesale and retail billing, orders and packing slips, approval (jakad),
karigar job work, manufacturing, stock with barcodes, accounts, GST, retail CRM and SMS.

Plain PHP 8.2 + MySQL 8, with no framework, Composer or Node. It runs on an online server and on
the local PC (XAMPP).

It matches the screens and printouts of SilkSol 2.00.1510, the software the business uses today,
so staff can switch without retraining, and it is GST-compliant in place of VAT/CST.

> **Status (2026-10-20):** planning. No application code yet. See `handover.md`.

## Documents

| | |
|---|---|
| Product requirements | [docs/project-documents/01-Product-Requirement-Document.md](docs/project-documents/01-Product-Requirement-Document.md) |
| Technical requirements | [docs/project-documents/02-Technical-Requirement-Document.md](docs/project-documents/02-Technical-Requirement-Document.md) |
| App flow | [docs/project-documents/03-App-Flow-Document.md](docs/project-documents/03-App-Flow-Document.md) |
| Design brief | [docs/project-documents/04-Design-Brief.md](docs/project-documents/04-Design-Brief.md) |
| Backend schema | [docs/project-documents/05-Backend-Schema.md](docs/project-documents/05-Backend-Schema.md) |
| Implementation plan | [docs/project-documents/06-Implementation-Plan.md](docs/project-documents/06-Implementation-Plan.md) |
| Architecture, rules, security | [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) |
| Installation | [docs/INSTALL.md](docs/INSTALL.md) |
| Form and report inventory | [docs/forms/FORM-INVENTORY.md](docs/forms/FORM-INVENTORY.md) |
| Glossary | [docs/GLOSSARY.md](docs/GLOSSARY.md) |
| SilkSol reference data | [docs/silksol-reference/README.md](docs/silksol-reference/README.md) |
| Rules for AI agents | [AGENTS.md](AGENTS.md), [CLAUDE.md](CLAUDE.md) |

## Planned layout

```
app/        Core/ Middleware/ Controllers/ Services/ Documents/ Reports/ Exceptions/
config/     app.php, menu.php, masters.php
database/   schema.sql, seed.sql, migrate.php, seeds/
routes/     web.php
views/      layouts/, partials/, <module>/, print/, errors/
public/     index.php, router.php, assets/
storage/    logs, sessions, uploads, backups, exports (not web-accessible)
cron/       daily.php, process-queue.php
tests/      run.php + tests
docs/       project documents, forms/, silksol-reference/
```
