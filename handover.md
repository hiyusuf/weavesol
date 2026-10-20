# Handover — WeaveSol

## What this is
Our own PHP + MySQL saree-trade software that matches SilkSol's screens, fields, flow, settings and
printouts, with GST in place of VAT/CST. See `README.md`.

## Where to read first
`AGENTS.md` → `docs/project-documents/01` to `06` → `docs/ARCHITECTURE.md` → `docs/forms/FORM-INVENTORY.md`.

## Current state (2026-10-20)
- Planning documents written (draft v0.1). **No application code.**
- SilkSol data file read (147 tables) and its report names listed: `docs/silksol-reference/`.
- Form inventory drafted: 121 entry forms (`F-xxx`, 18 of them "New" WeaveSol-only screens) and
  196 printed formats (`R-xxx`, 7 "New"). The SilkSol rows are "Not captured" because they come
  from data, not from the live screens.
- Planning, security, UI, UX and code reviews were run on the documents (2026-10-20), and their
  confirmed findings were fixed. The GST points the CA must confirm are decision D9 in `06`.
- **Phase 0 (screen walkthrough) is blocked:** the PC screen was locked during the session, so no
  SilkSol screen has been seen yet. SilkSol was running in its main window ("SilkSol [Demo Version]
  [2010 - 2011]").

## Decisions already made (don't re-litigate)
- Plain PHP 8.2 + MySQL 8, no framework / Composer / Node (owner, 2026-10-20).
- Runs on an online server and on this PC.
- Manufacturing and Retail CRM + SMS must work in version 1.
- GST-compliant; no VAT / CST.
- No old data to import.
- Project folder `E:\Yusuf\php\WeaveSol`.
- SilkSol is studied only by using it; no decompiling, no `Company.mdb`, no `.rpt` reuse.

## Open items / next steps
1. Owner: unlock the PC and leave SilkSol open so Phase 0 can run (`silksol-form-capture` skill).
2. Owner: decide D2 (which copy is live: online or PC), D7 (host, SMS provider), D8 (e-invoice
   API or JSON) in `06-Implementation-Plan.md`.
3. Owner / CA: confirm HSN codes and GST rates for the product range.
4. After Phase 0: update `03-App-Flow-Document.md` §2 with the exact SilkSol menu, update the
   keys in `04-Design-Brief.md` §6, fill the `doc_types` "Phase 0" codes in `05-Backend-Schema.md`
   §3, then ask for plan approval before Phase 1.
5. ~~Create the Git repository and remote.~~ Done 2026-10-20: `https://github.com/hiyusuf/weavesol.git`, branch `main`.
