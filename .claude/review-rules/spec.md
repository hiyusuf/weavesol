# WeaveSol - spec rules

Read by the `write-spec` skill.

## Where specs go
- Screen specs: `docs/forms/F-xxx-<short-name>.md` from `docs/forms/_TEMPLATE.md`, listed in `docs/forms/FORM-INVENTORY.md`. Use the existing ID from the inventory; add a new ID only for a screen found in Phase 0 or a new GST screen.
- Report specs: add a section to the matching `F-xxx` file or create `docs/forms/R-xxx-<name>.md` for a stand-alone report.
- Constitution: `.specify/memory/constitution.md`. A spec must not contradict it.

## Sources of truth
- `docs/project-documents/01` to `06`, `docs/ARCHITECTURE.md`, `docs/GLOSSARY.md`. Link to rules instead of restating them.

## Every screen spec must state
- The SilkSol menu path and caption, and how it opens (menu, button, key).
- Fields in Enter order with label, type / length / format, default, required, look-up, validation.
- Grid columns and calculations; footer charges, GST and totals.
- Buttons and keys, including hidden ones.
- **Stock impact** and **ledger impact** (Dr / Cr) of Save, edit, delete and cancel.
- **GST impact**: what changes from SilkSol's VAT/CST behaviour.
- **Schema impact**: tables and columns used; any new column with its `ALTER TABLE`.
- **Access**: menu permission key and actions.
- Print formats (`R-xxx`) offered from the screen.
- A parity checklist that can be ticked off side by side with SilkSol.

## Wording
- Use the business's own words from `docs/GLOSSARY.md`. Labels follow SilkSol captions.
