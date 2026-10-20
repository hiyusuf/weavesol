# App Flow Document (AFD) — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** how a user moves through WeaveSol, screen by screen.
>
> **Status:** the menu tree and form flows below come from SilkSol's data and report set. The
> **exact SilkSol menu order, captions, shortcut keys and button rows are captured in Phase 0** and
> then replace the "provisional" parts here. Each screen's own flow lives in `docs/forms/F-xxx-*.md`.

---

## 1. Site map

```
/login ─► /two-factor (if required) ─► /select-company ─► /select-year ─► / (main window)
                                                                        │
     Menu bar: Company · Masters · Purchase · Sales · Job Work · Manufacturing · Accounts ·
               Stock · CRM & SMS · Reports · GST · Utilities · Window · Help
                                                                        │
     Every menu item opens a screen inside the main window (an "entry window" or a "report window").
```

## 2. Menu tree (provisional; final order from Phase 0)

| Menu | Items |
|------|-------|
| **Company** | Select company · Create / edit company · Select financial year · New financial year · Year-end transfer · Lock date · Change password · Sign out |
| **Masters** | Account group · Schedule heads · Ledger / party · Opening balance · Item (saree) · Item type · Unit · Fabric · Colour · Pattern · Quality · Category · HSN / SAC · Purchase item (resham / zari) · Karigar · Karigar job rates · Job · Agent · Sub-agent · Salesman · Transporter / angadia · City · State · Designation · Department · Holiday · Standard narration · General master list · Bill charges · Posting settings · Series · Cheque layout · Reminder letter |
| **Purchase** | Purja (purchase bill) · Purchase invoice · Purchase return / debit note · Fabric purchase · Purchase item (resham / zari) purchase · Supplier order · Receive against supplier order · Purja payment · Old unpaid purja |
| **Sales** | Order · Packing slip · Sale invoice · Cash memo (retail) · Approval issue · Approval sale · Approval return · Credit note · Debit note · Proforma invoice · Despatch without order · Gate pass confirmation · Transport details |
| **Job Work** | Job issue · Job receive · Karigar bill · Karigar payment · Karigar payment return · Party job challan · Party job invoice · Party job receive |
| **Manufacturing** | Raw material · Raw material category · Formula (BOM) · Costing sheet (MRP) · Raw material purchase · Material issue · Production receipt |
| **Accounts** | Receipt · Payment · Journal · Contra · Expense voucher · Cash book entry · Petty cash (peti) · Bill-wise adjustment · Cheque issue · Cheque received / post-dated · Supplier payment · TDS · Bank reconciliation · Party reconciliation |
| **Stock** | Opening stock · Stock adjustment · Stock transfer · Labels · Barcode labels · Photo catalogue · Price list |
| **CRM & SMS** | Customer · Customer group · Family members · Address book contacts · Send SMS · SMS templates · SMS log |
| **Reports** | Documents · Accounts · Stock · Sales & orders · Job · Manufacturing · Stationery (address book, labels, envelopes) · Graphs |
| **GST** | GSTR-1 · GSTR-3B · HSN summary · E-invoice JSON · E-way bill JSON · Enter IRN / EWB number · Mark period filed · Purchase register (ITC) |
| **Utilities** | Settings · Users · Roles & rights · Backup · Restore · Check data / rebuild balances · Re-post documents · Import masters (CSV) · Audit log |

A user sees only the items their role allows. Hidden items are also blocked on the server.

## 3. Authentication flows

### 3.1 Sign in
1. User enters user name and password.
2. Wrong details: one general message ("User name or password is wrong"); attempts are throttled
   per account and per IP with growing delays ("Too many attempts. Try again in 2 minutes."), not
   a permanent lock, so nobody can lock the owner out on purpose.
3. If the user has two-factor sign-in (always for Admin and high-risk rights), the TOTP code screen
   follows. A lost phone: sign in with one of the 10 one-time recovery codes given at set-up, or
   another super-admin resets the user's two-factor (logged). If the only super-admin loses both,
   a CLI script on the server resets it (`php cron/reset-2fa.php <user>`, documented in INSTALL).
4. If the password must be changed (first sign-in or reset), the change-password screen follows.

### 3.2 Choose company and year
1. A list of the companies the user may open (SilkSol shows a "Company selection" box at start).
2. Then the financial year (default: the year containing today; the year last used by this user
   is listed first, so in April March bills still go to the old year). A date outside the chosen
   year is refused: "Date 02-04-2026 is outside the year 2025-26. Change the year from the Company menu."
3. Company and year show in the window title bar, as in SilkSol
   ("1. <Company> [ 2026 - 2027 ]"). Switching either is under the **Company** menu.

### 3.3 Sign out and time-out
- Sign out from the Company menu. If any open window has unsaved changes, WeaveSol lists them and
  asks before signing out.
- Two minutes before the inactivity limit a warning shows with a "Stay signed in" button.
- If the session has already ended (or the form token expired, 419), the typed form is kept in the
  page. A sign-in box opens over it, and after signing in the user presses Save again. Nothing is
  lost and nothing is saved twice.
- Closing or reloading the app window with unsaved changes asks first.

## 4. Standard entry-window flow (all masters and documents)

Confirmed per form in Phase 0; this is the expected common pattern.

1. The window opens in **view mode** on the last record (documents) or a blank search (masters).
2. **Add** (Alt+A / F2 — confirmed in Phase 0): fields clear, the number comes from the series,
   the date defaults to the last date used (or today), and the cursor goes to the first field.
3. **Enter** moves to the next field in SilkSol order. Shift+Enter goes back.
4. Look-up fields (party, item, karigar, agent, transporter, city…) open a pop-up list on a key
   press or when typed text doesn't match; type-ahead filters; Enter picks.
5. In item grids, Enter on the last column of a row starts a new row; a blank row ends the grid
   and moves to charges / footer.
6. **Save** (F10 / Ctrl+S — confirmed in Phase 0): Save locks until the server answers; every
   form carries a one-time submit token, so a double press can't create two documents or use two
   numbers. The server validates, numbers the document and posts ledgers and stock in one
   transaction. The status bar then shows "Sale invoice A/123 saved. Print? (Ctrl+P)". On errors,
   focus moves to the first wrong field or grid cell.
7. **Edit**: find a record (by number, date, party), change it, save; postings are rebuilt.
   Not allowed on or before the lock date.
8. **Delete / cancel** (F8): masters only when unused ("City 'Surat' is used by 12 parties and
   can't be deleted"). A document needs delete rights, a reason and a confirmation that names it:
   "Delete Purja 45 dated 12-05-2026 of ₹48,200? Its stock and ledger entries will be reversed."
   GST sale documents are **cancelled**, never deleted ("Cancel invoice A/123? The number stays and
   shows as cancelled."). Delete and cancel need two-factor if the user's role requires it.
9. **Navigation**: first / previous / next / last record, and find.
10. **Esc** closes the pop-up, then the window (asking first if there are unsaved changes).

## 5. Core business flows

### 5.1 Purchase (purja) to stock
1. Purchase › Purja: supplier, supplier bill number and date, items, quantities, rates, bill
   charges, GST.
2. Save → stock pieces get stock numbers; supplier ledger credited; purchase and input-tax ledgers
   debited.
3. Print → purja copy; Stock › Labels → barcode / saree labels for the new pieces.
4. Later: Purchase › Purja payment or Accounts › Payment, settled against the purja bill.

### 5.2 Order → packing slip → invoice → despatch → receipt
1. Sales › Order: party, items, quantity, rate, delivery date.
2. Sales › Packing slip: pick pieces against the order (or direct); bales with metres and pieces.
3. Sales › Sale invoice: pull the packing slip(s); add agent, charges, GST; save → stock out,
   party debited, sales and output-tax credited, order pending reduced.
4. Transport details: LR / bilty number, transporter, bales, weight, freight; print agent,
   angadia and office copies; e-way bill JSON if needed.
5. Accounts › Receipt: settle against the invoice (bill-wise). The outstanding report updates.

### 5.3 Approval (jakad)
1. Approval issue: goods go to a party on approval (stock moves to "on approval").
2. Approval sale: chosen pieces become a sale invoice (or cash memo).
3. Approval return: the rest comes back to stock.
4. The pending-approval report shows what is still out.

### 5.4 Retail counter sale
1. Sales › Cash memo: scan barcodes; pick or add the customer (CRM).
2. Payment split: cash / card / cheque; adjust returned goods.
3. Save → print the memo; SMS to the customer (if on).

### 5.5 Job work with a karigar
1. Job Work › Job issue: karigar, job (e.g. Polish), pieces or metres, rate, material
   (resham / zari by weight). Stock moves to "with karigar"; delivery challan printed.
2. Job Work › Job receive: against the issue; received, cut / rejected and returned quantities.
   Stock comes back. If the item's job route changes it to another item / stage, the grid shows
   "Received as: <item>" on that line before Save.
3. Job Work › Karigar bill: work amount from the receive lines × job rate, minus advance.
4. Job Work › Karigar payment / Accounts › Payment.
5. Reports: job delivery status, karigar history, karigar payment ledger, stock with karigar.

### 5.6 Manufacturing
1. Masters: raw material, department, formula (BOM) per item, costing sheet.
2. Raw material purchase → raw-material stock.
3. Material issue to a department or karigar (quantities from the formula, editable).
4. Production receipt of finished items → item stock at cost; consumption posted.
5. Reorder report from minimum and reorder levels.

### 5.7 Which payment screen to use
Purja payment, supplier payment and karigar payment are payment vouchers made for one kind of party:
they show that party's pending bills and fill the bill-wise settlement for you. Accounts › Payment is
the general voucher for any ledger. All of them post the same way. Phase 0 confirms whether SilkSol
keeps them as separate screens; WeaveSol keeps whatever SilkSol has.

### 5.8 Accounts
- Receipt / payment: account (cash, bank, petty cash), party, amount, cheque details, narration
  (standard narrations available), bill-wise settlement pop-up.
- Journal / contra: Dr and Cr lines that must balance.
- Cheques: issue → print on the bank layout; post-dated cheques show in the register until their date.
- Bank reconciliation: tick cleared items with the bank date.

### 5.9 Year-end
1. Company › New financial year: creates the next year.
2. Year-end transfer: first a **preview** (balances, pending bills, stock and open items that
   will move, with totals), then confirm with two-factor. Only balance-sheet ledgers carry their
   closing balance; the year's net profit / loss goes to the capital (or chosen) ledger; closing
   stock value becomes opening stock. Pending bills, stock pieces, open orders, approvals and
   karigar stock are carried forward. A result summary is shown and kept.
3. It can be run again until the old year is closed: "This replaces the openings made on
   12-04-2026. Openings typed by hand in the new year will be overwritten." Closing the old year
   locks it.

## 6. Report flow
1. Choose a report from the menu → filter panel (dates, party, item, agent, city, series…).
2. **Show**: on-screen table with totals; select a row (↑ / ↓) and press Enter, or click it, to
   open the source document. Esc returns to the report with filters and position kept.
3. **Print** (print view), **PDF**, **CSV / Excel**.
4. Document reprints: open the document → Print → choose the copy (office / agent / angadia /
   packing slip) and the format (A4, half page, with picture…).

## 7. System (automatic) flows
- Daily (cron or Windows Task Scheduler on the PC): birthday and anniversary SMS, payment-due SMS
  (if enabled), local backup, clean-up of expired sessions and old exports.
- Queue: SMS and e-mail are sent by `cron/process-queue.php`.

## 8. Error pages
- 403 (no rights), 404 (not found or out of scope), 423 (period locked), 500 (generic, no details
  in production).
- 419 (form expired) is never a full page inside an entry window: the window keeps the typed data
  and shows the sign-in box (§3.3). It is a full page only for non-window requests.
