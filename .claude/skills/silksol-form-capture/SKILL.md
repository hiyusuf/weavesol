---
name: silksol-form-capture
description: Capture one SilkSol screen (or a whole menu) into a WeaveSol form spec under docs/forms/ by using SilkSol as a normal user. Use during Phase 0 when asked to walk through, capture, document or record SilkSol forms, menus, settings or reports.
---

# Capture a SilkSol screen

Goal: a `docs/forms/F-xxx-<short-name>.md` file, from `docs/forms/_TEMPLATE.md`, that is complete
enough to build the WeaveSol screen without looking at SilkSol again.

## Rules
- Use SilkSol only through its own window: menus, buttons, keys, double-click, right-click.
- Never decompile, disassemble or resource-extract `C:\SilkSol\SilkSol.exe`, never open
  `C:\SilkSol\Company.mdb`, never open or copy `.rpt` files.
- Work in the demo company. Type only test data (e.g. party "Test Party 1"). Don't save entries
  unless the save effect must be recorded; note any saved test entry in the spec.
- If the PC is locked or SilkSol isn't showing, stop and tell the user. Don't start extra copies of
  SilkSol in a loop; close only copies you started.

## Steps
1. Find the form in `docs/forms/FORM-INVENTORY.md` (or give it the next free ID and add a row).
2. Open it from the menu. Screenshot the empty window (`docs/forms/screens/F-xxx-1.png`).
3. Press Add (or its key). Walk the fields with Enter, recording for each field, in order: the
   label, its type / length / format, default, whether it is required, the look-up it opens, and
   any message on bad input.
4. For grids: record the columns, how each total is worked out, and how the grid ends.
5. Try every button and the keys F1–F12, Alt / Ctrl combinations, double-click and right-click on
   fields and grids. Any window that opens is a form: give it its own `F-xxx` and capture it too.
6. Note what Save does: the number given, stock change, and ledger effect (check the ledger report).
7. Open Print: list every copy / format offered; print one sample to PDF in `docs/forms/prints/`.
8. Note the settings that change this screen.
9. Fill the template, set the inventory status to "Captured", and add a GST note where VAT/CST
   fields appear (they become GST fields).
10. Keep wording short; describe long SilkSol messages in your own words.

## For a whole menu
Record the menu tree in exact order with captions and shortcut keys first, then capture each item.
Update `docs/project-documents/03-App-Flow-Document.md` §2 at the end.
