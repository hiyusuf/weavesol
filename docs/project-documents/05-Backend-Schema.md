# Backend Schema — WeaveSol

_Version 0.1 (draft) · 2026-10-20_

> **What this is for:** the MySQL tables, what each holds, and the rules that keep the data right.
> `database/schema.sql` will be the single source of truth once it exists; this document explains it.

Conventions:
- InnoDB, `utf8mb4_unicode_ci`, `snake_case` names, `id BIGINT UNSIGNED AUTO_INCREMENT` primary keys.
- Money `DECIMAL(15,2)`; rates `DECIMAL(15,4)` where needed; quantity `DECIMAL(14,3)`; percent `DECIMAL(7,3)`.
  qty × rate is worked out with `bcmath` at scale 7 and rounded half-up to paise once, at the line amount.
- No nullable columns inside a UNIQUE key (MySQL treats NULLs as different): "none / all" is stored
  as `0`. The one deliberate exception is `documents.gst_doc_no`, where NULL means "not a GST
  document, so not part of this rule".
- No MySQL reserved words as column names (`key`, `to`, `order`, `desc`, `group`…).
- Real `DATE` / `DATETIME` columns (SilkSol stored several dates as text).
- Every business table has `company_id`; year-bound tables also have `fy_id`. Every query filters
  on them (see §4).
- `created_by`, `created_at`, `updated_by`, `updated_at` on every business table (not repeated below).
- Our schema is designed fresh. It does not copy SilkSol's tables. The mapping in §9 only shows
  where each SilkSol data area is covered, for the parity checks.

---

## 1. Data map (by area)

| Area | Tables |
|------|--------|
| Access and system | `users`, `roles`, `permissions`, `role_permissions`, `user_companies`, `login_attempts`, `user_totp`, `audit_log`, `settings`, `backups_log` |
| Company and year | `companies`, `financial_years`, `lock_dates` |
| Reference | `states`, `cities`, `units`, `hsn_codes`, `hsn_rate_slabs`, `holidays`, `narrations`, `designations`, `departments` |
| Accounts masters | `account_groups`, `ledgers`, `ledger_contacts`, `ledger_openings` |
| Item masters | `item_types`, `item_attributes` (fabric / colour / pattern / quality / category), `items`, `item_rate_history`, `item_job_routes`, `item_photos` |
| People | `agents`, `salesmen`, `karigars`, `jobs`, `karigar_job_rates`, `transporters` |
| Billing setup | `doc_types`, `number_series`, `bill_charges`, `bill_charge_postings`, `print_formats`, `cheque_layouts`, `reminder_letters` |
| Documents | `documents`, `document_lines`, `document_line_bales`, `document_charges`, `document_taxes`, `document_links`, `document_transport`, `document_gst_einvoice` |
| Stock | `stock_pieces`, `stock_movements`, `stock_balances` |
| Accounts | `ledger_entries`, `bill_refs`, `bill_settlements`, `cheques`, `bank_reco`, `tds_entries` |
| Manufacturing | `raw_materials`, `raw_material_categories`, `bom_headers`, `bom_lines`, `costing_sheets`, `costing_sheet_lines` |
| CRM and SMS | `customers`, `customer_groups`, `customer_family`, `sms_templates`, `message_queue`, `message_log` |

## 2. Tables

### 2.1 Access and system

| Table | Key columns |
|-------|-------------|
| `users` | `username` (unique), `full_name`, `mobile`, `email`, `password_hash`, `must_change_password`, `is_active`, `is_super_admin` (all companies: backup, restore, company create, user admin), `totp_required`, `last_login_at`. **No user is seeded:** the first super-admin is made on the first-run setup screen |
| `roles` | `name`, `is_system` |
| `permissions` | `code` (`masters.ledger.view`, `sales.invoice.add` …), `menu_key`, `action` (view/add/edit/delete/print/export/cancel/special) |
| `role_permissions` | `role_id`, `permission_id` |
| `user_companies` | `user_id`, `company_id`, `role_id` — a user's role can differ per company |
| `login_attempts` | `username`, `ip`, `success`, `attempted_at` (for throttling) |
| `user_totp` | `user_id`, `secret_encrypted`, `confirmed_at`, `recovery_codes_hash` |
| `audit_log` | `company_id`, `user_id`, `entity`, `entity_id`, `action` (add/edit/delete/cancel/print/login), `before_json`, `after_json`, `ip`, `user_agent`, `at`. `AuditService` drops never-logged fields before writing: `password_hash`, TOTP secret, recovery codes, and masks PAN, bank account and phone numbers |
| `settings` | `company_id` (0 = global), `setting_key`, `setting_value`; UNIQUE (`company_id`, `setting_key`) — includes SilkSol "configuration" switches (fabric/colour/pattern/quality on, max lines per entry = 150, sale barcode on, photo common, financial-date check …) |
| `backups_log` | `file_name`, `size`, `made_by`, `made_at`, `kind` (manual/scheduled) |

### 2.2 Company and year

| Table | Key columns |
|-------|-------------|
| `companies` | `code` (1, 2, …), `name`, `print_name`, `address1..3`, `city_id`, `state_id`, `pin`, `phones`, `email`, `website`, `gstin`, `gst_reg_type`, `pan`, `bank_name`, `bank_account`, `ifsc`, `signatory`, `logo_path`, `invoice_terms`, `is_active` |
| `financial_years` | `company_id`, `label` ("2026-27"), `start_date`, `end_date`, `is_closed`, `carried_forward_at` |
| `lock_dates` | `company_id`, `fy_id`, `lock_upto` |

### 2.3 Reference

| Table | Key columns |
|-------|-------------|
| `states` | `name`, `gst_state_code` (2 digits), `short_code` |
| `cities` | `name`, `state_id`, `district` |
| `units` | `name` (Pcs., Mtrs., Kg., Gm.), `uqc` (GST unit code: PCS, MTR, KGS, GMS), `decimals` |
| `hsn_codes` | `code`, `description`, `is_service`, `default_gst_rate` |
| `hsn_rate_slabs` | `hsn_id`, `from_date`, `price_upto` (per-piece value limit, NULL = no limit), `gst_rate` |
| `holidays` | `company_id`, `date`, `remark` |
| `narrations` | `company_id`, `text` (SilkSol keeps 4 lines × 25 characters) |
| `designations`, `departments` | `company_id`, `name` |

### 2.4 Accounts masters

| Table | Key columns |
|-------|-------------|
| `account_groups` | `company_id`, `code` (2 digits, as SilkSol: 10–94), `name`, `parent_id`, `nature` (DR/CR), `statement` (TRADING / PL / BS / APPROPRIATION), `page_side`, `sort_order`, `show_in_trial`, `schedule_head_id`, `is_system` |
| `schedule_heads` | `company_id`, `name` — schedule headings for the balance sheet (SilkSol `SCHHEAD`) |
| `ledgers` | `company_id`, `account_no` (6 digits: group code + serial, kept for familiarity), `name`, `group_id`, `address1..3`, `city_id`, `district`, `area`, `state_id`, `pin`, `std_code`, `phone_office`, `phone_resi`, `mobile`, `fax`, `email`, `website`, `contact_person`, `reference`, `agent_id`, `transporter_id`, `gstin`, `gst_reg_type`, `pan`, `credit_limit`, `credit_days`, `less_percent`, `grade`, `on_hold`, `invoice_mode`, `freight_mode`, `pslip_wise_invoice`, `discount_per_piece`, `bill_header`, `bill_footer`, `remarks`, `photo_path`, `is_active`, `is_system` (Cash, Sales, Purchase, GST ledgers…) |
| `ledger_contacts` | `ledger_id`, `name`, `designation_id`, `address1..2`, `city`, `phone`, `mobile`, `other`, `photo_path` |
| `ledger_openings` | `ledger_id`, `fy_id`, `amount`, `dr_cr`; opening bills go to `bill_refs` |

### 2.5 Item masters

| Table | Key columns |
|-------|-------------|
| `item_types` | `company_id`, `name` (Saree, Than, Scarf, Dress, Suit), `unit_id`, `qty_decimals`, `photo_path` |
| `item_attributes` | `company_id`, `kind` (FABRIC / COLOUR / PATTERN / QUALITY / CATEGORY / GENERAL — GENERAL covers SilkSol's general `MASTER` list), `name` |
| `items` | `company_id`, `code` (unique per company), `name`, `item_type_id`, `unit_id`, `hsn_id`, `fabric_id`, `colour_id`, `pattern_id`, `quality_id`, `category_id`, `pattern_text1..4`, `label_text`, `item_code`, `rate_code`, `purchase_rate`, `sale_rate`, `mrp`, `less_amount`, `net_rate`, `issue_rate`, `margin_percent`, `warp`, `weft`, `reed`, `pick`, `width`, `weight`, `variation`, `supplier_ledger_id`, `remarks`, `is_active` |
| `item_rate_history` | `item_id`, `effective_date`, `purchase_rate`, `sale_rate` |
| `item_job_routes` | `item_id`, `seq`, `job_id`, `karigar_id`, `work`, `remark` |
| `item_photos` | `item_id`, `path`, `is_main` |

### 2.6 People

| Table | Key columns |
|-------|-------------|
| `agents` | `company_id`, `parent_id` (sub-agent), `name`, `type`, `address1..3`, `city_id`, `district`, `state_id`, `country`, `pin`, `phone`, `contact_person`, `commission_percent`, `email`, `website`, `remarks`, `photo_path` |
| `salesmen` | `company_id`, `name`, `address1..3`, `city_id`, `state_id`, `phone`, `mobile`, `commission_percent` |
| `karigars` | `company_id`, `code`, `name`, `son_of`, `address`, `city`, `phone`, `mobile`, `photo_path`, `remarks`, `ledger_id` (for payments) |
| `jobs` | `company_id`, `name`, `short_name`, `default_rate`, `sac_hsn_id` |
| `karigar_job_rates` | `karigar_id`, `item_id` (0 = all items), `job_id`, `effective_date`, `rate`, `discount`, `discount_percent`, `due_days`, `remark`, `highlight` |
| `transporters` | `company_id`, `name`, `kind` (TRANSPORT / ANGADIA / COURIER / BY_HAND), `address1..3`, `city_id`, `state_id`, `pin`, `phone`, `contact_person`, `email`, `gstin`, `remarks` |

### 2.7 Billing setup

| Table | Key columns |
|-------|-------------|
| `doc_types` | `code` (see §3), `name`, `module`, `stock_effect`, `posts_ledger`, `gst_document`, `silksol_trmd` (reference only) |
| `number_series` | `company_id`, `fy_id`, `doc_type_code`, `name` ("Main"), `prefix`, `next_no`, `width`, `is_default` — row-locked when numbering. On save: prefix + width ≤ 16 characters, only `A–Z a–z 0–9 / -`, and the prefix must differ from every other GST series of the company, so no two GST documents share a number in a year |
| `bill_charges` | `company_id`, `slot` (1–9), `name`, `calc` (PERCENT / AMOUNT / PER_QTY), `sign` (ADD / LESS), `affects_taxable_value`, `display_format`, `is_active` |
| `bill_charge_postings` | `company_id`, `doc_type_code`, `bill_charge_id`, `ledger_id`, `side` (DR/CR) — replaces SilkSol `SETTING` / `OTHERCONFIG` |
| `print_formats` | `company_id`, `doc_type_code`, `name` (Office copy, Agent copy, Angadia copy, Packing-slip copy, A4, Half, With picture…), `view_key` (a key looked up in a fixed list of files under `views/print/`, never a path), `paper`, `positions_json`, `is_default` |
| `cheque_layouts` | `company_id`, `bank_name`, `width_mm`, `height_mm`, `positions_json` (date, payee, amount figures, amount words, a/c payee) |
| `reminder_letters` | `company_id`, `name`, `header_text`, `footer_text` |

### 2.8 Documents (one model for every trade document)

| Table | Key columns |
|-------|-------------|
| `documents` | `company_id`, `fy_id`, `doc_type_code`, `series_id`, `doc_no` (formatted; given only when the document is first posted — a DRAFT has no number), `gst_doc_no` (copy of `doc_no` for GST document types, else NULL), `gst_filed_period` (set by "Mark period filed"; blocks edit), `doc_date`, `ledger_id` (party / supplier), `karigar_id`, `department_id`, `customer_id` (retail), `agent_id`, `sub_agent_id`, `salesman_id`, `agent_comm1_percent`, `agent_comm2_percent`, `party_bill_no`, `party_bill_date`, `order_ref`, `due_days`, `due_date`, `place_of_supply_state_id`, `is_reverse_charge`, `total_qty`, `total_pcs`, `total_mtr`, `gross_amount`, `discount_amount`, `charges_amount`, `taxable_amount`, `cgst`, `sgst`, `igst`, `cess`, `round_off`, `net_amount`, `cash_amount`, `card_amount`, `cheque_amount`, `cheque_detail`, `returned_goods_amount`, `advance_amount`, `narration`, `remark`, `status` (DRAFT / POSTED / CANCELLED), `cancel_reason`, `printed_count`, `sms_sent` |
| `document_lines` | `document_id`, `line_no`, `line_kind` (ITEM / RAW / ACCOUNT / TEXT), `item_id`, `raw_material_id`, `ledger_id` (account lines of vouchers), `debit`, `credit` (account lines), `stock_piece_id`, `description`, `hsn_id`, `qty`, `pcs`, `mtr`, `weight`, `rate`, `mrp`, `discount_percent`, `discount_amount`, `amount`, `charges_share`, `taxable_value`, `gst_rate`, `cgst`, `sgst`, `igst`, `job_id`, `karigar_id`, `parcel_no`, `bale_no`, `cut_qty`, `reject_qty`, `remark`, `delivery_date`, `is_closed` |
| | CHECK: exactly the column that matches `line_kind` is set (ITEM → `item_id`, RAW → `raw_material_id`, ACCOUNT → `ledger_id`, TEXT → none). The foreign keys on these columns are `ON DELETE RESTRICT`, because MySQL 8 refuses a CHECK on a column whose foreign key uses SET NULL or CASCADE |
| `document_line_bales` | `document_line_id`, `bale_no`, `mtr`, `pcs` (packing slips / job issue) |
| `document_charges` | `document_id`, `bill_charge_id`, `rate`, `amount`, `taxable` |
| `document_taxes` | `document_id`, `gst_rate`, `taxable_value`, `cgst`, `sgst`, `igst` (rate-wise summary for print and GSTR) |
| `document_links` | `from_line_id`, `to_line_id`, `qty` — order→packing slip→invoice, issue→receive, approval issue→sale / return, supplier order→purja. Pending qty = line qty − linked qty |
| `document_transport` | `document_id`, `transporter_id`, `lr_no`, `lr_date`, `bales`, `cases`, `total_kg`, `freight_rate`, `freight_amount`, `through`, `destination`, `challan_no`, `pslip_no`, `po_no`, `terms`, `vehicle_no`, `distance_km` |
| `document_gst_einvoice` | `document_id`, `irn`, `ack_no`, `ack_date`, `signed_qr`, `ewb_no`, `ewb_date`, `ewb_valid_upto`, `json_generated_at` |

### 2.9 Stock

| Table | Key columns |
|-------|-------------|
| `stock_pieces` | `company_id`, `stock_no` (printed on the label / barcode, unique per company), `item_id`, `purchase_line_id`, `cost_rate`, `mrp`, `sale_rate`, `qty`, `mtr`, `location` (IN_HAND / RESERVED (on a packing slip) / WITH_KARIGAR / ON_APPROVAL / PARTY_JOB (a party's own goods for job work — never counted in our stock value) / IN_TRANSIT (inter-company transfer) / SOLD / RETURNED / CONSUMED), `location_ref_id` (0 = none), `fy_id_created` |
| `stock_movements` | `company_id`, `fy_id`, `document_line_id`, `stock_kind` (ITEM / RAW), `stock_ref_id` (item or raw material id), `stock_piece_id` (0 = none), `location`, `location_ref_id` (karigar / party; 0 = none), `qty_in`, `qty_out`, `value`, `moved_on` |
| `stock_balances` | `company_id`, `fy_id`, `stock_kind`, `stock_ref_id`, `location`, `location_ref_id` (all NOT NULL; 0 = none), `qty`, `value`; UNIQUE on the six key columns — a summary that can be rebuilt from movements |

### 2.10 Accounts

| Table | Key columns |
|-------|-------------|
| `ledger_entries` | `company_id`, `fy_id`, `entry_date`, `ledger_id`, `debit`, `credit`, `document_id` (source), `voucher_line_no`, `narration`, `cheque_id`, `bank_cleared_on` |
| `bill_refs` | `company_id`, `fy_id`, `ledger_id`, `document_id` (or opening bill), `ref_no`, `ref_date`, `amount`, `dr_cr`, `due_date` |
| `bill_settlements` | `bill_ref_id`, `settled_by_document_id`, `amount`, `settled_on` — outstanding = bill − settlements |
| `cheques` | `company_id`, `direction` (ISSUED / RECEIVED), `ledger_id`, `bank_ledger_id`, `cheque_no`, `cheque_date`, `amount`, `payee_name`, `status` (PENDING / DEPOSITED / CLEARED / BOUNCED / CANCELLED), `document_id`, `printed_at` |
| `bank_reco` | `ledger_entry_id`, `bank_date`, `statement_ref` |
| `tds_entries` | `company_id`, `fy_id`, `document_id`, `ledger_id`, `nature`, `section`, `period_from`, `period_to`, `amount`, `tds_rate`, `tds_amount`, `deposit_date`, `challan_no`, `bank`, `pan` |

Vouchers (receipt, payment, journal, contra, petty cash) are `documents` rows with doc types in §3.
Their Dr / Cr lines are `document_lines` with `ledger_id` columns (account lines), turned into
`ledger_entries` on save.

### 2.11 Manufacturing

| Table | Key columns |
|-------|-------------|
| `raw_material_categories` | `company_id`, `name`, `remarks` |
| `raw_materials` | `company_id`, `code`, `name`, `detail`, `category_id`, `unit_id`, `hsn_id`, `min_level`, `max_level`, `reorder_level`, `purchase_rate`, `mrp`, `is_active` (opening stock via the opening-stock document) |
| `bom_headers` / `bom_lines` | `item_id`, `version`, `is_active` / `bom_header_id`, `raw_material_id`, `qty`, `unit_id` |
| `costing_sheets` / `costing_sheet_lines` | `item_id`, `name`, `cost`, `margin_percent`, `mrp`, `remark`, `photo_path` / `costing_sheet_id`, `seq`, `job_id`, `work`, `amount`, `remark` |

### 2.12 CRM and SMS

| Table | Key columns |
|-------|-------------|
| `customer_groups` | `company_id`, `name`, `remarks` |
| `customers` | `company_id`, `code`, `joined_on`, `group_id`, `title`, `first_name`, `last_name`, `address1..2`, `area`, `city`, `district`, `state_id`, `pin`, `dob`, `anniversary`, `wish_dob`, `wish_anniversary`, `card_no`, `card_status`, `card_issue_date`, `card_expiry_date`, `valid_from`, `valid_to`, `total_purchase`, `discount_percent`, `current_balance`, `phone_resi1..3`, `phone_office1..3`, `mobile1..3`, `email`, `website`, `sms_opt_out` |
| `customer_family` | `customer_id`, `name`, `dob`, `phone` |
| `sms_templates` | `company_id`, `name`, `event` (SALE / DUE / BIRTHDAY / ANNIVERSARY / BULK), `body`, `dlt_template_id`, `is_active` |
| `message_queue` | `company_id`, `channel` (SMS / EMAIL), `recipient`, `template_id`, `body`, `status`, `attempts`, `next_try_at` |
| `message_log` | `queue_id`, `provider_ref`, `status`, `response_code`, `sent_at` (no message body for OTP-type texts) |

## 3. Document types

| Code | Name | Stock | Ledger | GST | SilkSol `TRMD` |
|------|------|-------|--------|-----|----------------|
| `PURJA` | Purja (purchase bill) | in | yes | input | 3 |
| `PUR_INV` | Purchase invoice | in | yes | input | 13 |
| `PUR_RET` | Purchase return / debit note to supplier (SilkSol "Dr.N.": posts against Purchase) | out | yes | input reversal | 6 |
| `FAB_PUR` | Fabric purchase | in | yes | input | Phase 0 |
| `ACC_PURCHASE` | Purchase item (resham / zari) purchase | raw in | yes | input | Phase 0 |
| `SUP_ORDER` | Supplier order | — | no | — | Phase 0 |
| `ORDER` | Customer order | — | no | — | Phase 0 |
| `PSLIP` | Packing slip | reserve | no | — | Phase 0 |
| `SALE_INV` | Sale invoice (tax invoice; prints as "Bill of supply" when every line is exempt or the company is composition) | out | yes | output | 4 |
| `CASH_MEMO` | Cash memo (retail) | out | yes | output (B2C; place of supply = company state) | 1 |
| `APP_ISSUE` | Approval issue (jakad) | move → on approval | no | delivery challan | Phase 0 |
| `APP_SALE` | Approval sale (chosen pieces become a sale; own approval cash book via an "Approval cash" ledger) | on approval → sold | yes | output | Phase 0 |
| `APP_RETURN` | Approval return | move ← on approval | no | — | Phase 0 |
| `CR_NOTE` | Credit note / sales return | in | yes | output reversal | 5 |
| `SALE_DR_NOTE` | Debit note to a customer (extra charge on a past invoice) — only if SilkSol has it | — | yes | output | Phase 0 |
| `PROFORMA` | Proforma invoice | — | no | — | Phase 0 |
| `GATE_PASS` | Gate pass | — | no | — | Phase 0 |
| `DESPATCH` | Despatch without order | — (stock moves on the invoice) | no | — | Phase 0 |
| `JOB_ISSUE` | Job issue to karigar | move → karigar | no | delivery challan | Phase 0 |
| `JOB_RECEIVE` | Job receive | move ← karigar | no | — | Phase 0 |
| `JOB_BILL` | Karigar bill | — | yes | input GST only if the karigar is registered (SAC 9988); TDS where it applies; no reverse charge by default (CA to confirm) | Phase 0 |
| `PJOB_CHALLAN` / `PJOB_RECEIVE` / `PJOB_INV` | Party job work | PARTY_JOB location (not our stock) | invoice only | output (SAC) | Phase 0 |
| `RM_PURCHASE` / `RM_SALE` | Raw material purchase / sale | raw in / out | yes | input / output | Phase 0 |
| `MAT_ISSUE` | Material issue | raw out | consumption | — | Phase 0 |
| `PROD_RECEIPT` | Production receipt | item in | yes (stock value) | — | Phase 0 |
| `OPENING_STOCK` / `STOCK_ADJ` | Stock | in / ± | adj. only | — | Phase 0 |
| `TRANSFER_OUT` / `TRANSFER_IN` | Inter-company stock transfer (a pair, one in each company, saved together) | out → in transit / in transit → in | yes (inter-company ledgers) | tax invoice if the GSTINs differ, else delivery challan | Phase 0 |
| `RECEIPT` / `PAYMENT` / `JOURNAL` / `CONTRA` / `PETTY` / `EXP_VOUCHER` / `CASH_BOOK` | Vouchers (expense voucher and daily cash book entry as in SilkSol) | — | yes | — | Phase 0 |
| `KAR_PAYMENT` / `KAR_PAY_RETURN` / `SUP_PAYMENT` / `PURJA_PAYMENT` | Payments | — | yes | — | Phase 0 |

The posting rule and the stock rule for each type sit in its `app/Documents/<Type>.php` definition
and are tested.

**Inter-company transfer.** `StockTransferService` is the only code that works in two companies at
once. The user needs rights in both; it writes `TRANSFER_OUT` in the source company and
`TRANSFER_IN` in the target, in one transaction, matching items by code. If SilkSol's "central item
master" option (`SilkSol.ini [ItemMaster] Central`) is on, saving an item in the master company
copies it to the other companies. There is no shared item table, so every other query stays scoped
to one company.

## 4. Who can see what

- A user works inside one company and one year at a time (session context). Every repository
  method takes `company_id`, and `fy_id` where the table has it. Fetching a record from another
  company returns 404.
- Rights are `permissions` per menu item and action, given through roles per company. They are
  checked by middleware on every route and again inside services for sensitive actions (delete,
  cancel, year-end, lock date, user admin).
- MySQL has no row-level security, so this scoping is done in code and covered by tests.

## 5. Integrity rules

- A document, its lines, charges, taxes, ledger entries, stock movements and bill refs are saved
  in **one transaction**. If any part fails, nothing is saved.
- `ledger_entries` per document must balance (sum of debits = sum of credits), checked before commit.
- Document numbers come from `number_series` under `SELECT … FOR UPDATE`, only when a document is
  first posted (drafts have no number); gap-free per series per year.
- A `stock_piece` can be in only one place; its row is locked when it moves. Negative stock is
  blocked unless the setting allows it.
- **No numbered document is ever deleted.** A posted document is **cancelled**: it keeps its number,
  shows as cancelled, and its postings, stock movements and bill refs are removed in the same
  transaction (the document no longer counts on its own date). Only an unnumbered DRAFT can be deleted.
- Cancel is refused while receipts or payments are settled against the bill ("Remove the 2
  settlements first"), after the GST period is marked filed (issue a credit / debit note
  instead), and once an IRN exists unless the IRN was cancelled on the portal (allowed within 24
  hours) and that is recorded first.
- Edit is refused once an IRN or e-way bill exists or the period is marked filed.
- No add / edit / cancel on or before `lock_dates.lock_upto`.
- Unique keys: `(company_id, doc_type_code, series_id, fy_id, doc_no)`, plus `(company_id, fy_id,
  gst_doc_no)` for GST documents (`gst_doc_no` = `doc_no` for GST types, NULL otherwise, so one
  number is never used twice under the GSTIN in a year), `(company_id, code)` on items,
  `(company_id, stock_no)` on pieces, `(company_id, account_no)` on ledgers.
- CHECK constraints: amounts ≥ 0 where they must be, `dr_cr IN ('DR','CR')`, `gst_rate IN (0, 0.1, 0.25, 1.5, 3, 5, 12, 18, 28, 40)`.

## 6. Where files live

| What | Where |
|------|-------|
| Item, karigar, ledger and agent photos; company logo | `storage/uploads/<company>/<kind>/<random>.jpg` (served by a controller after a rights check) |
| Backups | `storage/backups/` (local) or a download only (online) |
| Exports | `storage/exports/` (deleted after 24 h) |
| Logs | `storage/logs/` |

## 7. Sensitive data and protection

- Passwords: bcrypt / argon2id. TOTP secrets encrypted with `APP_KEY`.
- PAN, GSTIN, bank account and phone numbers are business data; they are never written to logs.
- Backups contain every company and the password hashes: download and restore are for a
  **super-admin** only, with a fresh two-factor code, and are logged. Backup files are signed with
  an HMAC of `APP_KEY`; restore refuses an unsigned or changed file (see `docs/ARCHITECTURE.md`).

## 8. Backups and retention

- Local PC: daily SQL backup to `BACKUP_PATH`, kept `BACKUP_KEEP_DAYS` days.
- Online: the host's daily backup plus a manual download before year-end.
- Financial records are kept at least 8 years (GST and Income Tax record rules).

## 9. SilkSol data coverage (for parity checks only)

| SilkSol area (tables in `Data.mdb`) | WeaveSol tables |
|------|------|
| `AC_GROUP`, `ACMAST`, `ACMASTDETAIL`, `ACMASTOPEN`, `ACMONTH` | `account_groups`, `ledgers`, `ledger_contacts`, `ledger_openings` (monthly totals come from `ledger_entries`) |
| `SAREE`, `SAREEDETAIL`, `SAREEJOBDETAIL`, `FABRIC`, `COLOUR`, `PATTERN`, `QUALITY`, `TBLCATEGORY`, `UNIT`, `iimage`, `STOCKPIC` | `items`, `item_rate_history`, `item_job_routes`, `item_attributes`, `units`, `item_photos` |
| `KARIGAR`, `JOB`, `JOBDETAIL`, `RATEHISTORY` | `karigars`, `jobs`, `karigar_job_rates` |
| `TBLAGENTMAST`, `TBLAGENTMASTSUB`, `AGENT`, `SALESMAN`, `TBLTRANSMAST`, `ANGADIA` | `agents`, `salesmen`, `transporters` |
| `EXPENSES`, `EXPENSESN`, `SETTING`, `OTHERCONFIG`, `SERIES`, `AUTOVNO`, `CODEDETAIL`, `CONFIGURATION` | `bill_charges`, `bill_charge_postings`, `number_series`, `settings` |
| `SALES`, `STOCK`, `STOCKD`, `EXP_INV`, `EXP_INV_NAME`, `INVDETAIL`, `inword`, `TBLTERMSLINK`, `SALEPURLINK` | `documents`, `document_lines`, `document_line_bales`, `document_charges`, `document_transport`, `document_links` |
| `ORDER*`, `PSLIP*` | `documents` (`ORDER`, `SUP_ORDER`, `PSLIP`) + `document_links` |
| `STOCKI`, `STOCKO`, `STOCKJ*`, `STOCKC`, `STOCKO*`, `SAREEBL`, `SAREEOPEN`, `P_ITEM`, `P_STOCK`, `P_*` | `stock_pieces`, `stock_movements`, `stock_balances`, `raw_materials` |
| `SALESJ`, `PAYMENTJ*`, `PAYDETAILJ*`, `KARRCDLINKKARINV`, `JOBDETAILPARTY`, `SALESPJOB`, `STOCKPJOB`, `STOCKJOB` | `documents` (job types) + `document_links` |
| `RECPAY*`, `CSMAST*` (incl. `CSMASTAPP*` approval cash, `CSMASTPETI` petty cash), `RECEIPT*`, `PAYMENT*`, `PAYDETAILSUP`, `PURJAPAY`, `PURJAOUT`, `MCHEQUE`, `TDS`, `EXP`, `EXPTRANS` | `documents` (vouchers, incl. `EXP_VOUCHER`, `CASH_BOOK`, `PETTY`; approval and petty cash are their own cash ledgers), `ledger_entries`, `bill_refs`, `bill_settlements`, `cheques`, `tds_entries`, `document_charges` |
| `SCHHEAD` | `schedule_heads` |
| `MASTER` | `item_attributes` (kind GENERAL) — purpose confirmed in Phase 0 |
| `TBLTRANSACTION` | `document_lines` (line_kind TEXT) |
| `TBLRawMast`, `TBLSTOCKRAW`, `TBLSTOCKORAW`, `TBLSALESRAW`, `TBLEXPTRANS*`, `TBLPISSU*`, `MMATERIAL`, `MCATEGORY`, `MDEPARTMENT`, `MFORMULA`, `MLINK`, `MSALES`, `MSTOCK`, `MRPMASTER`, `MRPDETAIL` | `raw_materials`, `raw_material_categories`, `departments`, `bom_*`, `costing_sheet*`, `documents` (manufacturing types) |
| `CUSTOMERMASTER`, `CUSTGRPMASTER`, `FAMILYMEMBER`, `ADDMASTER`, `ADDGRPMASTER`, `ADDFAMILYMEMBER`, `SMS` | `customers`, `customer_groups`, `customer_family`, `sms_templates`, `message_*` |
| `CITY`, `STATE`, `HOLIDAY`, `NARRATION`, `DESIGNATION`, `REM_LETTER`, `SAREELABEL` | `cities`, `states`, `holidays`, `narrations`, `designations`, `reminder_letters`, `print_formats` |
| `TMP*`, `BALTRSETTING` | Not stored: reports query live data; year-end uses `financial_years` |

## 10. Changing the schema

Edit `database/schema.sql`, add the matching `ALTER TABLE` statements to the change note for
phpMyAdmin, update this document, and run the tests. No migration files.
