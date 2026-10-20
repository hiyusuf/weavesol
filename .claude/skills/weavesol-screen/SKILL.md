---
name: weavesol-screen
description: Build one WeaveSol screen (master, document, voucher or report) from its docs/forms spec so it matches SilkSol. Use from Phase 4 onward when asked to build, implement or add a WeaveSol form, entry screen, master or report.
---

# Build a WeaveSol screen

## Before starting
- Read `AGENTS.md`, the screen's `docs/forms/F-xxx-*.md` spec, `docs/ARCHITECTURE.md` and the
  tables in `docs/project-documents/05-Backend-Schema.md`.
- If the spec status is not at least "Reviewed", stop and say so. Don't guess fields.

## Steps
1. **Schema:** check that every field in the spec has a column. If not, edit `database/schema.sql`,
   write the `ALTER TABLE`, update `05-Backend-Schema.md`.
2. **Permission:** add the permission keys (`<module>.<form>.<action>`) to the seed and the menu
   entry to `config/menu.php` in SilkSol's menu position.
3. **Definition / service:**
   - Simple look-up master: add it to `config/masters.php` (generic screen).
   - Other master: a repository + service with validation and the "can't delete when used" rule.
   - Document or voucher: a definition class in `app/Documents/` (header fields, line columns,
     charges, stock effect, posting rule, series, print formats). Business rules stay in services.
4. **Controller + routes:** read input, call the service, render; routes with `auth` + `perm:`.
5. **View:** fields in the spec's Enter order and grouping; `e()` on every value; data attributes
   for `entry-form.js` / `line-grid.js` / `lookup.js`; no inline script or style.
6. **Print views** for each `R-xxx` the spec lists, under `views/print/`.
7. **Tests:** validation, scope (404 for another company's record), permission (403), and for
   documents: GST maths, balanced postings, stock movement, numbering, edit and cancel reversal.
8. **Parity check:** fill section 10 of the spec side by side with SilkSol; set the inventory
   status to "Built", then "Parity OK" when it passes.
9. Update `handover.md` if the phase status changes.

## Done means
`php tests/run.php` passes, the keyboard flow works without a mouse, the parity checklist passes,
and the security checklist in `docs/ARCHITECTURE.md` still holds.
