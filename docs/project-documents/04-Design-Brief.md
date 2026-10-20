# Design Brief — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** how WeaveSol looks and feels, so every screen is built the same way.

---

## 1. Design principle: same work, our own look

- **Same as SilkSol:** what is on each screen, where it sits, the field order, the labels, the
  defaults, the keys and the flow. Staff who know SilkSol must find each field where they expect it.
  The position of field groups on each form follows the Phase 0 screenshot (`docs/forms/F-xxx`).
- **Our own:** colours, fonts, icons, the logo, button styling and every image. Nothing is copied
  from SilkSol's graphics, logo or name.
- It feels like desktop software in the browser: dense, fast and keyboard-first, not a website.

## 2. Brand

- Name: **WeaveSol**. Wordmark only for now (no image logo until one is approved).
- Personality: dependable, quick, no-nonsense: a ledger book, not a shop window.

## 3. Colour palette

Defined as CSS custom properties on `:root` in `public/assets/css/app.css`, with a dark theme
under `@media (prefers-color-scheme: dark)` and `[data-theme="dark"]`.

| Token | Light | Dark | Use |
|-------|-------|------|-----|
| `--bg` | `#F4F5F7` | `#16181D` | Window background |
| `--surface` | `#FFFFFF` | `#1F2228` | Forms, panels, grids |
| `--surface-alt` | `#EEF1F5` | `#262A31` | Read-only fields, grid header, zebra rows |
| `--text` | `#1C2430` | `#E6E9EE` | Body text |
| `--text-muted` | `#5B6676` | `#9AA4B2` | Labels, hints |
| `--border` | `#C9D0DA` | `#3A404A` | Grid lines and panel dividers only |
| `--field-border` | `#7D8898` | `#6B7686` | Borders of editable fields and buttons (≥ 3:1 on surface and surface-alt) |
| `--primary` | `#1F5FAE` | `#5B9BEA` | Menu bar, primary buttons, focus ring |
| `--primary-ink` | `#FFFFFF` | `#0D1420` | Text on primary, accent, danger and warning fills |
| `--accent` | `#0A7364` | `#3CC2AE` | Totals, saved state |
| `--focus` | `#FFE9A8` | `#4A4220` | Background of the field that has focus (helps fast entry) |
| `--danger` | `#C2362F` | `#F0716A` | Errors, delete, cancelled stamp |
| `--warning` | `#8F5600` | `#E9A23B` | Credit-limit and stock warnings |
| `--success` | `#0A7364` | `#3CC2AE` | Success toast border and icon |
| `--hover` | `#E3E8EF` | `#2C3139` | Hover on menu items, buttons and grid rows |
| `--selected` | `#D6E4F7` | `#22344F` | Selected grid row / active look-up item (text stays `--text`) |
| `--disabled-text` | `#8A94A3` | `#6E7785` | Disabled controls (exempt from contrast, but always also shown with `cursor: not-allowed` and no border) |

Rules:
- Text contrast at least WCAG AA (4.5:1); field borders, focus ring and icons at least 3:1.
- Status is never shown by colour alone: an error also has an icon and text. Debit / credit
  amounts are labelled "Dr" / "Cr" as in SilkSol, not just coloured.
- Inside a focused field only `--text` is used (in dark mode muted, danger and primary text fail on
  `--focus`); field errors show under the field, not inside it.
- Read-only fields: `--surface-alt` background **and** no border **and** a small lock icon, so they
  never look editable.
- Text on a coloured fill (stamps, delete button, badges) uses `--primary-ink` in dark mode and
  white in light mode.
- No hard-coded colours anywhere; add a token instead.

## 4. Typography

- System font stack: `"Segoe UI", system-ui, -apple-system, Roboto, Arial, sans-serif`.
- Numbers in grids and totals use `font-variant-numeric: tabular-nums` and are right-aligned.
- Sizes: body and fields 14 px; grid 13 px; window title 15 px semibold; report headings 16 px.
- Line height 1.3 in fields and grids, 1.45 in text blocks. Labels 400 weight, `--text-muted`.
  Focus ring 2 px `--primary` with 1 px offset.
- No web fonts, icon fonts, CSS frameworks or CDN files: system fonts and the inline SVG sprite
  only (works offline on the local PC). No inline `style=` or `<script>` (strict CSP).
- Print: 10–11 pt body, 9 pt grid lines; sizes per format in `views/print/`.

## 5. Layout

```
┌ Title bar: "1. <Company> [ 2026 - 2027 ]"  · user · date ──────────────────────────┐
│ Menu bar: Company  Masters  Purchase  Sales  Job Work  Manufacturing  Accounts …     │
├──────────────────────────────────────────────────────────────────────────────────────┤
│  ┌ Entry window: "Sale Invoice" ───────────────────────────────── [_] [□] [×] ┐      │
│  │ Header fields (as SilkSol)                                                  │      │
│  │ Line grid                                                                   │      │
│  │ Charges + GST + totals                                                      │      │
│  │ Button row: Add  Edit  Delete  Save  Cancel  Find  Print  ◄ ► Exit         │      │
│  └─────────────────────────────────────────────────────────────────────────────┘      │
│ Status bar: key hints (F2 Add · F3 Find · Ctrl+S Save · Esc Close) · messages         │
└──────────────────────────────────────────────────────────────────────────────────────┘
```

- **Windows:** each screen opens as a window inside the main area, like SilkSol's MDI windows.
  Several can be open; a Window menu lists them.
- **Screen widths:** entry screens are designed for 1366 × 768 and up. Below 1024 px windows open
  full-size; below 768 px labels stack above their fields and the line grid scrolls sideways
  inside its own box. Reports, look-ups and the dashboard work down to 360 px with no page-level
  horizontal scroll.
- Spacing scale: 4, 8, 12, 16, 24 px. Field height 28 px. Grid row height 26 px.
- Forms use the SilkSol field grouping from the Phase 0 screenshot, laid out on a CSS grid.

## 6. Keyboard (the most important part)

**Supported mode:** Chrome or Edge with WeaveSol installed as an app ("Install app" → its own
window, no address bar or browser menu). In that mode the page can take F1–F12, Ctrl+S, Ctrl+P and
most Alt+letter keys. The browser always keeps Ctrl+W, Ctrl+T, Ctrl+N, Ctrl+Tab and Alt+F4: WeaveSol
never uses them, and it warns before the window closes or reloads with unsaved changes.

**Key map — draft.** Phase 0 records SilkSol's real keys. Each one is then tested in the installed
app window and recorded in `docs/forms/KEYMAP.md` as "SilkSol key → WeaveSol key", with an
alternative wherever the browser can't give up a key.

| Key | Action |
|-----|--------|
| Enter | Next field (skips read-only fields); in the last grid column, new row |
| Shift+Enter / Shift+Tab | Previous field |
| F2 | Add |
| F6 | Edit |
| F7 | Find |
| F10 / Ctrl+S | Save (locked while saving) |
| Ctrl+P / F9 | Print |
| F8 | Delete / Cancel document (always confirms) |
| Ctrl+PgUp / Ctrl+PgDn | Previous / next record (plain PgUp / PgDn scroll lists and grids) |
| F4 or Alt+↓ in a look-up field | Open the look-up list (plain ↓ in a grid moves down a row) |
| Insert (in a look-up list) | Add a new master record from the list |
| Ctrl+Shift+Delete (grid) | Delete line (confirms; Ctrl+Z restores the last deleted line) |
| Ctrl+F6 / Ctrl+Shift+F6 | Next / previous open window |
| F5 | Taken by WeaveSol: refresh the current list, never a page reload |
| Esc | Close pop-up → cancel edit → close window (asks before losing changes) |

- When a window closes, focus goes back to the window that was active before it.
- Reports: ↑ / ↓ move the selected row, Enter opens its document, Esc returns to the report
  with its filters and scroll position kept.
- Barcode scanning: the scan field treats the scanner's Enter as "add this piece and stay here".
  A piece scanned twice, already sold or not in stock gives a message and a beep, and is not added.
- Every button shows its key in its label (underlined letter or a "(F2)" hint).
- Focus is always visible (focus ring + `--focus` background).
- Dates are typed as `ddmmyy` or `dd-mm-yyyy`; `+` / `-` changes a day; `t` gives today.
- Numbers: typed without commas; shown in Indian grouping (12,34,567.00).

## 7. Components

| Component | Notes |
|-----------|-------|
| Entry field | Label on the left (desktop style), field on the right; required fields marked with `*` |
| Look-up field | Text field + list button; pop-up list with search, keyboard selection and an "Add new" key for masters |
| Line grid | Fixed header; columns per document definition; running totals in the footer |
| Charges panel | 9 bill-charge rows (only the enabled ones show), each as % or amount, with tax effect |
| GST panel | Taxable value, CGST / SGST / IGST by rate, round-off, net amount, amount in words |
| Button row | Add, Edit, Delete, Save, Cancel, Find, Print, navigation, Exit |
| Report window | Filter panel → table → totals → Print / PDF / CSV / Excel |
| Save result | Status bar message that stays until the next action: "Sale invoice A/123 saved. Print? (Ctrl+P)" |
| Errors on save | Listed in a summary at the top of the window (`role="alert"`); focus moves to the first wrong field or grid cell; each field shows its own message |
| Message | Toast for success (`role="status"`, `aria-live="polite"`, stays 4 s, stays while hovered or focused); inline message under the field for errors; a dialog only for confirmations |
| Stamp | "CANCELLED", "ON APPROVAL", "DRAFT" on documents and prints |

## 8. Screen states

Every screen and report designs for: empty (no records yet — say what to add), loading (skeleton
rows; no spinner over the whole window), saving (Save and Print disabled with "Saving…" until the
server answers, so a second press can't save twice), error (what went wrong and what to do), no
rights (403 message), period locked (lock date shown), offline / session expired (sign in again
without losing the typed form if possible), and in look-up pop-ups "no match" (with "Add new" when
the user may add that master).

## 9. Print design
- Print views use `@page` sizes: A4 portrait / landscape, A5 / half page, label sheets, envelopes,
  cheque (bank-specific size).
- The company header comes from the company master (name, address, GSTIN, phone, logo).
- Each document type lists its copies (Office, Agent, Angadia, Packing-slip) and formats; the
  information on each matches the SilkSol printout of the same name.
- Pre-printed stationery mode: field positions set in millimetres per format in Settings.
- Tax documents (invoice, cash memo, credit / debit note, bill of supply) always print: GSTIN of
  seller and buyer, place of supply, HSN per line, taxable value, tax by rate (CGST / SGST / IGST),
  round-off, amount in words, reverse-charge flag, signature.
- Print views always use the light tokens (`@media print`), whatever the screen theme. Stamps and
  totals must read clearly on a black-and-white printer (bold text or a border, not colour alone).

## 10. Accessibility and motion
- Every field has a real `<label>`. Errors are linked to their field (`aria-describedby`).
- Grids use `role="grid"` with keyboard navigation.
- No animation beyond 150 ms fades; nothing moves under `prefers-reduced-motion`.

## 11. Voice and wording
- Use the business's own words: Purja, Karigar, Angadia, Bilty / LR, Than, Packing slip. See
  `docs/GLOSSARY.md`. Menu and screen captions copy SilkSol's captions once Phase 0 records them.
  Until then write "Approval (Jakad)" and "Petty cash (Peti)", because both words still need the
  owner to confirm them.
- Labels match SilkSol captions (recorded in Phase 0). Messages are short and say what to do:
  "Party is over its credit limit of ₹50,000. Save anyway?"
