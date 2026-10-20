# WeaveSol - UI review rules

Read by the `review-ui` skill. Source: `docs/project-documents/04-Design-Brief.md`.

## Parity
- Field grouping and order on each entry screen follow its `docs/forms/F-xxx` screenshot. Flag a screen that moves fields around without a reason in the spec.
- The look is our own: flag any image, logo, icon or colour scheme copied from SilkSol.

## Tokens
- Colours only from the `:root` tokens in `public/assets/css/app.css` (light and dark). Flag hard-coded colours.
- Text contrast at least WCAG AA. Status never by colour alone (Dr / Cr labels, icons with errors).

## Density and layout
- Field height 28 px, grid rows 26 px, spacing scale 4/8/12/16/24. Numbers right-aligned with tabular figures and Indian grouping.
- Entry screens are designed for 1366 × 768 and up; below 1024 px windows open full-size; reports and look-ups work down to 360 px without page-level horizontal scroll (grids may scroll inside their box).

## States
- Every screen has empty, loading, saving, error, no-rights, period-locked and offline / session-expired states; look-ups have a "no match" state. Flag a missing one.
- Field borders use `--field-border` (≥ 3:1); read-only fields have no border and a lock icon; text in a focused field is `--text` only.
- Print views force the light tokens.

## Constraints
- No new fonts, icon sets or CSS frameworks; system fonts only, inline SVG sprite for icons. No CDN.
- No inline `style=` that repeats an existing class; no inline `<script>`.

## Print
- Print views use `@page` sizes per format; company header from the company master; GST fields present on tax documents. Flag a tax document missing GSTINs, place of supply, HSN, tax by rate, or amount in words.

## Motion
- Fades up to 150 ms only; nothing moves under `prefers-reduced-motion`.
