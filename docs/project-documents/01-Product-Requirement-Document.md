# Product Requirement Document (PRD) — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** what WeaveSol must do and for whom. The screen-by-screen detail lives in
> `docs/forms/` (one file per form). The technical "how" lives in `02-Technical-Requirement-Document.md`.

---

## 1. Product summary

WeaveSol is web-based trading, job-work, manufacturing and accounting software for a saree business
(wholesale, retail counter, job work through karigars, and in-house manufacturing). It runs on an
online server and on the owner's own PC (XAMPP), and is built in plain PHP and MySQL.

WeaveSol is our own software. Its **reference model** is SilkSol 2.00.1510 ("A Complete Saree Trade
Software", Predicate Software Services Pvt. Ltd.), which the business uses today. WeaveSol must offer
the same modules, entry screens, fields, field order, defaults, numbering, settings and printed
documents, so staff can move across without retraining. The only planned difference is tax: **WeaveSol
is GST-compliant and drops VAT/CST.**

How SilkSol is studied (binding on everyone working on this project):

- SilkSol is studied **only as a user would**: by running it, opening every menu, screen, button and
  shortcut key, and recording what is seen and printed (`docs/forms/`).
- The company data file `C:\SilkSol\1011\Comp1\Data.mdb` was read to understand the data the screens
  hold (`docs/silksol-reference/`).
- Nobody decompiles `SilkSol.exe`, extracts its code, resources or hidden forms, opens the protected
  `Company.mdb`, or copies its Crystal Report (`.rpt`) files, logo, name or text. Every screen and
  report is rebuilt by us.

## 2. Goals

| # | Goal | Measured by |
|---|------|-------------|
| G1 | **Screen parity:** every SilkSol form has a WeaveSol screen with the same fields, order, defaults and keys | Each row in `docs/forms/FORM-INVENTORY.md` marked "Parity OK" |
| G2 | **Print parity:** every printed document and report gives the same information and layout | Side-by-side print check per format |
| G3 | **GST compliance:** tax invoices, credit/debit notes, HSN summary, GSTR-1 / GSTR-3B data, e-invoice and e-way bill data | CA review of sample documents and returns |
| G4 | **Keyboard speed:** a 10-line sale invoice can be entered without the mouse as fast as in SilkSol | Timed test by an existing operator |
| G5 | **Runs in both places:** the same code runs on the online server and on the local XAMPP PC | Install test on both |
| G6 | **Correct books:** every bill, receipt and job entry posts to the ledgers automatically | Trial balance, P&L and balance sheet tally on test data |

## 3. Users and roles

Roles are configurable. Rights are set per menu item and per action (view, add, edit, delete, print,
export). Starter roles:

| Role | Typical work |
|------|--------------|
| **Owner / Admin** | Everything, including company, year, users, rights, settings, year-end and backups |
| **Accountant** | Vouchers, cheques, bank reconciliation, TDS, GST reports, all financial reports |
| **Billing Operator** | Orders, packing slips, sale invoices, cash memos, approval, transport details |
| **Purchase / Stock Clerk** | Purja (purchase), purchase returns, stock adjustment, labels and barcodes |
| **Job-Work Clerk** | Karigar issue / receive / bill / payment, job reports |
| **Production Clerk** | Raw material, formulas, material issue, production receipt |
| **Counter Sales (Retail)** | Retail cash memo with barcode scan, customer (CRM) entry |
| **Viewer** | Reports only |

A user can be allowed into one or more companies. A **super-admin** (the owner) works across all
companies and alone can create companies, back up and restore. Super-admins, Admins and anyone with
delete, cancel, year-end, lock-date, user-admin, backup or restore rights must use two-factor
sign-in (full rule: `docs/ARCHITECTURE.md`, control 9).

## 4. Features

Every form below has a form ID (`F-xxx`) in `docs/forms/FORM-INVENTORY.md`. The fields listed here
come from SilkSol's data. Field order, labels and keys are confirmed in Phase 0 (screen walkthrough)
and recorded in each form file.

### 4.1 Company, year and users
- Create, select and edit a company: name, address, state, GSTIN, PAN, phones, e-mail, logo, bank
  details, signatory, invoice terms, and the default print header and footer.
- Financial years (April–March) per company. Selecting a year at sign-in limits every screen to it.
- Year-end: carry forward ledger balances, outstanding bills, stock (pieces and quantities), open
  orders, pending approvals and stock lying with karigars.
- Users, roles, the rights matrix, password change, sign-in history, two-factor sign-in.
- Lock date: no entries can be added or edited on or before it.

### 4.2 Masters
- **Account groups** (Trading, P&L and Balance Sheet groups with Dr/Cr nature; starter set seeded).
- **Ledger / party master**: name, group, address (3 lines), city, district, area, state, PIN, phones,
  STD code, mobile, fax, e-mail, website, contact person, reference, agent, default
  angadia/transporter, **GSTIN, GST registration type, PAN**, credit limit, credit days, "less %",
  grade, hold flag, invoice mode, freight option, packing-slip-wise invoice option, discount per
  piece, custom bill header and footer, remarks, photo, and extra contact persons. Opening balance
  per year.
- **Item (saree) master**: code, name, item type, HSN code, unit, fabric, colour, pattern, quality,
  category, label text, purchase rate, sale rate, MRP, less amount, net rate, item code / rate code,
  warp, weft, reed, pick, width, weight, variation, supplier, photo, remarks, job route (jobs in
  order, with karigar and work), and rate history.
- **Item types** (Saree, Than, Scarf, Dress, Suit…) with unit (Pcs./Mtrs.) and number format.
- **Look-up masters**: unit (with GST UQC code), fabric, colour, pattern, quality, category, city,
  state (with GST state code), designation, department, holiday list, standard narrations.
  `CONFIGURATION` switches (fabric / colour / pattern / quality on or off) are kept as settings.
- **Karigar master**: name, son of, address, city, phone, mobile, photo, remarks; job-rate list per
  karigar, item and job with date, discount, due days and history.
- **Job master**: job name, short name, default rate (Polish, Embroidery, Hand Dye…).
- **Agent master** (with sub-agents): name, type, address, city, district, state, country, PIN,
  phone, contact person, commission %, e-mail, website, remarks, photo.
- **Salesman master**: name, address, city, state, phones, commission %.
- **Transporter / angadia master**: name, address, contact, phones, remarks.
- **Bill charges** (SilkSol "expenses", 9 slots): name (Commission ×2, Hand Dye, Polish,
  Embroidery, Rate Diff., Packing, Freight, Others), entry as % or amount, add or less, whether
  it is part of the GST taxable value, and the ledger to post to for each document type.
- **Series**: invoice number series per document type (for example "Main"), with prefix and the
  next number. GST rule: unique, consecutive, at most 16 characters, restarting each year.
- **Reminder letter** text (header and footer), **SMS templates** (with DLT template ID).
- **Cheque layouts** per bank (10 banks are set up in SilkSol: HDFC, HDFC-Quick, Bank of Baroda,
  Bank of India, ICICI, IDBI, Kotak Mahindra, Oriental Bank of Commerce, SBI, Union Bank of India),
  with adjustable field positions.
- **HSN / SAC master** with GST rate, including value-based slabs (for example apparel above or
  below a price limit).

### 4.3 Purchase
- **Purja (purchase bill)** from a weaver or supplier: party, bill number and date, our number,
  item lines (item, quantity, pieces, metres, rate, discount, remarks), piece / stock numbers,
  9 bill charges, GST, round-off, net amount, due days, transport (LR number, date, transporter).
  Each piece gets a stock number for labels and barcodes.
- **Purchase invoice**, **purchase return / debit note**, **fabric purchase**.
- **Supplier order** and receipt against order (pending quantity tracking).
- **Purja payment** and old unpaid purja tracking.

### 4.4 Sales
- **Customer order** with delivery date, remarks, close flag, pending quantity, and links to packing
  slips and invoices.
- **Packing slip** against an order or direct, with metres and pieces per bale.
- **Sale invoice (tax invoice)**: series, number, date, party, agent (commission % ×2), salesman,
  order number, packing slip numbers, item lines (direct, by barcode / stock number, or pulled from
  a packing slip or order), discount, 9 bill charges, GST (CGST+SGST or IGST by place of supply),
  round-off, net amount, cases / bales, weight, through (transporter), destination, LR / bilty
  number and date, freight, terms, due date, remarks, cancel flag. One invoice can print as
  office copy, agent copy, angadia copy and packing-slip copy.
- **Cash memo / retail counter sale**: barcode scan, customer (CRM) look-up, cash / card / cheque
  split, returned-goods adjustment, SMS to the customer.
- **Approval (jakad)**: issue goods on approval, convert to sale, take returns. Approval goods have
  their own cash book.
- **Credit note** (sales return) and **debit note**, with GST.
- **Proforma invoice**, **despatch without order**, **gate pass confirmation**, **LR-not-found check**.
- **Transport details** per bill: LR number, LR date, number of bales, total kg, rate, freight,
  challan number, packing-slip number, PO number.

### 4.5 Job work (karigar)
- **Job issue** to a karigar: items or pieces, job (polish, embroidery…), rate, metres and pieces,
  parcel and bale numbers, material issued (resham, zari by weight).
- **Job receive** from a karigar: against the issue, quantity received, cut / rejected, return.
- **Karigar bill**: work amount, advance, payment, due days, transport, bilty.
- **Karigar payment** and advance, payment return, karigar payment ledger.
- **Party job work**: job work done for outside parties (challan, invoice, receive).
- **Job delivery status**, karigar history, stock lying with each karigar.

### 4.6 Manufacturing (must work in version 1)
- Raw material master (category, unit, minimum / maximum / reorder level, purchase rate, MRP,
  active flag, opening stock), department master.
- **Formula / bill of materials**: per item, the raw materials and quantity.
- **Costing sheet (MRP master)**: item, cost build-up by job and work, margin %, MRP, photo.
- Raw material purchase, **material issue** to a department or karigar, **production receipt**
  of finished items, raw-material stock and reorder report.

### 4.7 Accounts
- **Receipt, payment, journal and contra vouchers**, with cash, bank and petty cash ("peti") books.
- **Bill-wise settlement** of receipts and payments against invoices (outstanding by bill).
- **Cheques**: issue register, received / post-dated register, cheque printing on bank layouts,
  counterfoil.
- **Supplier payment** with discount per piece / %.
- **TDS** entries (nature, period, rate, challan, bank) and TDS on bank interest; TCS where it applies.
- **Bank reconciliation** and **party reconciliation / ledger confirmation**.
- Every trade document posts to the ledgers automatically. Users never key the double entry for a bill.

### 4.8 Stock, labels and catalogue
- Opening stock (by item and by piece), stock adjustment, stock transfer between companies.
- **Labels**: saree labels in 5 sizes, labels with invoice number, barcode labels (wholesale and
  retail), price list, MRP labels.
- **Photo catalogue**: item photos on order, packing slip and catalogue printouts.

### 4.9 Retail customer (CRM) and SMS (must work in version 1)
- Customer master: title, first and last name, group, address, area, city, PIN, state, district,
  phones (residence / office / mobile ×3), e-mail, website, birthday, anniversary, loyalty card
  number, status, issue and expiry dates, total purchase, discount, current balance, family members
  with birthdays.
- **SMS**: on sale, on payment due, birthday and anniversary greetings, and bulk messages to a
  group, using DLT-registered templates through a configurable SMS gateway. Every message is logged.

### 4.10 Reports and printed documents
About 190 formats, grouped below. The full list is in `docs/silksol-reference/silksol_report_list.txt`;
each one gets a row in the form inventory.
- **Documents:** invoice copies, cash memo copies, credit / debit note copies, packing slip (A4,
  half, full, with picture, catalogue), order, supplier order, purja, job challan, job invoice,
  proforma, voucher, bank deposit slip, certificate, bilty.
- **Accounts:** cash book, day book, journal, ledger (normal, continuous, detail, cash,
  confirmation, multi-year, reconciliation), trial balance (summary and detail), profit & loss,
  balance sheet, outstanding (bill-wise, monthly, summary, detail), party statistics, reminder letter.
- **Stock:** stock in hand, stock register, stock ledger, item-wise stock, item ledger (party, job,
  agent commission), purchase history, purchase not issued, price list.
- **Sales and orders:** sale register, sale search, order reports (party-wise, item-wise,
  supplier-wise), order search, packing-slip search, payment search.
- **Job:** job issue / receive detail, karigar history, karigar payment ledger, job delivery
  status, item summary by job party, voucher search (job).
- **Stationery:** address book (5 variants), address labels, envelopes (5 variants), agent labels,
  cheques, graphs.
- **GST (new):** GSTR-1 (B2B, B2C large, B2C small, credit / debit notes, HSN summary, documents
  issued), GSTR-3B summary, e-invoice JSON, e-way bill JSON, purchase register with input tax.
- Every report: screen view, print, PDF, and CSV / Excel export, with filters for date, party, item,
  agent, city and series.

### 4.11 Utilities
- Backup and restore (download a full SQL backup; scheduled backups on the local PC).
- Data check / rebuild balances, re-post documents to the ledgers.
- Import masters from CSV.
- Text-mode printing for dot-matrix printers (SilkSol uses `PRNTEXT.BAT`), as an option.
- Audit log viewer: who added, edited or deleted what, and when (SilkSol keeps user, mode, date,
  time and computer name on vouchers).

## 5. GST requirements (replace VAT/CST)

- Party: GSTIN (format and checksum validated), registration type (regular, composition,
  unregistered, consumer, SEZ, overseas), state.
- Item: HSN code, GST rate, UQC unit.
- Place of supply decides CGST+SGST (same state) or IGST (other state).
- Bill charges marked "taxable" (for example packing, freight) are added to the taxable value in
  proportion to the lines. Trade discount is deducted before tax.
- Tax invoice, bill of supply (composition / exempt), credit note and debit note show the fields GST
  requires: GSTIN of both parties, place of supply, HSN, taxable value, rate and amount of each tax,
  amount in words, reverse-charge flag, signature.
- Invoice numbering: unique, consecutive per series per year, at most 16 characters.
- Cancelled invoices keep their number and show as cancelled. Issued invoices are never deleted.
- E-invoice (IRN, acknowledgement number, signed QR) and e-way bill: version 1 creates the JSON for
  upload to the government portal and stores the returned IRN / EWB number. A direct API link (GSP)
  is a later option.
- Job work sent to karigars: delivery challan (no tax), with the job-work return period tracked.
- Old SilkSol tax fields (CST, ST, KST, TIN) are not carried over.

## 6. Out of scope (version 1)

- Importing old SilkSol data. There is no live data. A CSV master import is provided instead.
- Two-way sync between the local PC copy and the online server (see open questions in `06`).
- Direct GST portal API (GSP), payment gateway, mobile app.
- Payroll.

## 7. Acceptance criteria (key)

1. Every row in `docs/forms/FORM-INVENTORY.md` is built and marked "Parity OK" after a side-by-side
   check with SilkSol.
2. A sale invoice, purja, credit note, karigar issue / receive / bill, receipt and journal entered in
   WeaveSol post the same ledger effect as in SilkSol (except tax, which follows GST).
3. Trial balance, P&L and balance sheet tally on the test data set.
4. GST invoice and GSTR-1 / 3B figures are checked by the business's CA.
5. The full keyboard flow (Enter, look-up keys, save, print) works without a mouse on every entry screen.
6. The same build installs and runs on the local XAMPP PC and on the online server.
7. The security checklist in `docs/ARCHITECTURE.md` passes.
