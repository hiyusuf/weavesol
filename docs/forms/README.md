# Form specs — WeaveSol

One file per screen, so each screen is built exactly once and checked against SilkSol.

- `FORM-INVENTORY.md` — every form (`F-xxx`) and printed format (`R-xxx`), with status.
- `_TEMPLATE.md` — copy it to `F-xxx-<short-name>.md` when capturing a screen.
- `screens/` — screenshots taken during the Phase 0 walkthrough (`F-xxx-1.png`, `F-xxx-2.png`…).
- `prints/` — one sample PDF per SilkSol report, printed from the demo company (`R-xxx.pdf`).

## Status values

| Status | Meaning |
|--------|---------|
| Not captured | Expected from SilkSol's data, not yet seen on screen |
| Captured | Spec file written from the live screen, with screenshot |
| Reviewed | Owner checked the spec |
| Built | WeaveSol screen exists |
| Parity OK | Side-by-side check passed (fields, order, defaults, keys, effect, print) |
| Not in SilkSol | Expected from the data but not found on screen; drop it or turn it into "New" |
| New | A WeaveSol-only screen or report (GST, security, utilities). It is never captured from SilkSol; its spec is written from the PRD and reviewed by the owner |

## Capture rules (Phase 0)
- Study SilkSol only through its own screens: menus, buttons, keys, double-click, right-click.
- Never decompile or extract `SilkSol.exe` resources, never open `Company.mdb`, never copy `.rpt` files.
- Use the demo company only. Do not enter real customer data.
- Write what you see: captions, the Enter order, defaults, messages (in your own words where a
  message is long), and what Save changes.
