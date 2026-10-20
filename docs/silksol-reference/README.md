# SilkSol reference data

Read on 2026-10-20 from the SilkSol install at `C:\SilkSol` on the owner's PC, to understand what
data each SilkSol screen holds. **Reference only:** WeaveSol's own schema is in
`docs/project-documents/05-Backend-Schema.md` and is designed fresh, not copied from here.

| File | What it is | How it was made |
|------|------------|-----------------|
| `silksol_db_schema.txt` | All 147 tables and their columns in the demo company data file `C:\SilkSol\1011\Comp1\Data.mdb` (FY 2010-11, company 1), with row counts. Type codes are OLE DB: 130 text (length), 5 double, 3 long, 2 integer, 17 byte, 7 date, 11 yes/no, 128 binary | Read from a copy of the file through the 32-bit Jet OLE DB driver |
| `silksol_settings_and_masters.txt` | Contents of the small setting and master tables: transporters, posting settings, configuration, series, units, expense heads, item types, agents, jobs, reminder letter, chart of accounts | Same |
| `silksol_report_list.txt` | Names of the 179 report files and 10 cheque layouts (189 formats; the list also shows one helper `.exe` in that folder, which was not run) | File names only; the `.rpt` files were not opened |

Not read, on purpose:
- `SilkSol.exe` — not decompiled or resource-extracted.
- `C:\SilkSol\Company.mdb` — protected (company list and licence); not opened.
- `C:\SilkSol\Install\*` — vendor's blank install databases.
- The `.rpt` Crystal Report files.

Account groups found (`AC_GROUP`, 40 rows) — WeaveSol seeds its own standard chart in the same
structure: 10 Trading A/c, 11 Opening Stock, 12 Purchase, 13 Trading Expenses, 14 Sales, 15 Closing
Stock, 20 Profit & Loss A/c / Gross Profit, 21 Income, 22 Administrative Expenses, 23 Selling &
Distribution, 24 Financial Expenses, 25 Depreciation, 29 P/L Appropriation, 30 Source of Fund,
31 Partners' Capital, 39 Net Profit, 40 Secured Loan, 41 Term Loan, 42 Cash Credit Limit,
50 Unsecured Loan, 51 Friends & Relatives, 60 Current Liabilities & Provisions, 61 For Goods, 62 For
Expenses, 63 For Tax, 64 Advances Payable, 70 Fixed Assets, 71 Sundry Assets, 80 Current Assets,
81 Sundry Debtors, 82 Closing Stock, 83 Cash & Bank, 84 Investments, 85 Claims Receivable,
90 Loans & Advances, 91 Advance Tax, 92 Advance to Staff, 93 Advance to Others, 94 Security Deposits.
