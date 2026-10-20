# Form & Report Inventory — WeaveSol

_Version 0.1 · 2026-10-20 · built from SilkSol's data tables and report files. Phase 0 confirms,
adds (including screens that only open from buttons or keys) and removes rows._

"Data" = the SilkSol tables the screen reads or writes (`docs/silksol-reference/silksol_db_schema.txt`).

## Entry forms

| ID | Module | Form | Data | Status |
|----|--------|------|------|--------|
| F-001 | Company | Company selection (start-up) | Company.mdb (not opened) | Not captured |
| F-002 | Company | Create / edit company | Company.mdb (not opened) | Not captured |
| F-003 | Company | Financial year select / new year | folder per year | Not captured |
| F-004 | Company | Year-end balance transfer | BALTRSETTING, ACMASTOPEN, SAREEOPEN, RECPAYOPEN, RECEIPTOPEN, STOCKOPJOB | Not captured |
| F-005 | Company | Users / passwords / rights | RECPAYDETAIL.USERNAME (user names seen) | Not captured |
| F-006 | Company | Lock date / financial-date check | Setup.ini FinancialDate | Not captured |
| F-007 | Company | First-run setup (super-admin, one-time token) | — | New |
| F-008 | Company | Sign-in, two-factor, recovery codes, time-out re-sign-in | — | New |
| F-009 | Company | Roles and rights matrix | — | New |
| F-010 | Masters | Account group | AC_GROUP | Not captured |
| F-011 | Masters | Ledger / party master | ACMAST | Not captured |
| F-012 | Masters | Party contact persons | ACMASTDETAIL | Not captured |
| F-013 | Masters | Opening balance | ACMASTOPEN, ACMAST.OP_AMOUNT | Not captured |
| F-014 | Masters | Item (saree) master | SAREE, SAREEDETAIL, iimage | Not captured |
| F-015 | Masters | Item job route | SAREEJOBDETAIL | Not captured |
| F-016 | Masters | Item type | SAREE.TYPE / STYL | Not captured |
| F-017 | Masters | Unit | UNIT | Not captured |
| F-018 | Masters | Fabric | FABRIC | Not captured |
| F-019 | Masters | Colour | COLOUR | Not captured |
| F-020 | Masters | Pattern | PATTERN | Not captured |
| F-021 | Masters | Quality | QUALITY | Not captured |
| F-022 | Masters | Category | TBLCATEGORY, MCATEGORY | Not captured |
| F-023 | Masters | Karigar master | KARIGAR | Not captured |
| F-024 | Masters | Karigar job rates / rate history | RATEHISTORY | Not captured |
| F-025 | Masters | Job master | JOB, JOBDETAIL | Not captured |
| F-026 | Masters | Agent master | TBLAGENTMAST, AGENT | Not captured |
| F-027 | Masters | Sub-agent master | TBLAGENTMASTSUB | Not captured |
| F-028 | Masters | Salesman | SALESMAN | Not captured |
| F-029 | Masters | Transporter | TBLTRANSMAST | Not captured |
| F-030 | Masters | Angadia | ANGADIA | Not captured |
| F-031 | Masters | City | CITY | Not captured |
| F-032 | Masters | State | STATE | Not captured |
| F-033 | Masters | Designation | DESIGNATION | Not captured |
| F-034 | Masters | Department | MDEPARTMENT | Not captured |
| F-035 | Masters | Holiday list | HOLIDAY | Not captured |
| F-036 | Masters | Standard narration | NARRATION | Not captured |
| F-037 | Masters | Expenses / bill charges | EXPENSES, EXPENSESN | Not captured |
| F-038 | Masters | Posting settings (charges → ledgers) | SETTING, OTHERCONFIG | Not captured |
| F-039 | Masters | Series | SERIES, AUTOVNO | Not captured |
| F-040 | Masters | Reminder letter | REM_LETTER | Not captured |
| F-041 | Masters | Configuration (fabric/colour/pattern/quality, max lines) | CONFIGURATION, CODEDETAIL | Not captured |
| F-042 | Masters | Purchase item (resham, zari) | P_ITEM | Not captured |
| F-043 | Masters | Cheque book / bank layout | MCHEQUE, ChequeBook\*.rpt | Not captured |
| F-044 | Masters | HSN / SAC (new for GST) | — | New |
| F-045 | Masters | General master list | MASTER | Not captured |
| F-050 | Purchase | Purja | SALES (TRMD 3), STOCK, EXP_INV, STOCKD | Not captured |
| F-051 | Purchase | Purchase invoice | SALES (TRMD 13), STOCK | Not captured |
| F-052 | Purchase | Purchase return / debit note | SALES, STOCK | Not captured |
| F-053 | Purchase | Fabric purchase | STOCK, SAREE (fabric) | Not captured |
| F-054 | Purchase | Supplier order | ORDERSUP, ORDERDETAILSUP | Not captured |
| F-055 | Purchase | Receive against supplier order | ORDERLINKSUP, ORDERLINKOSUP, ORDERLINKSUPJ | Not captured |
| F-056 | Purchase | Purja payment | PURJAPAY | Not captured |
| F-057 | Purchase | Old unpaid purja | PURJAOUT | Not captured |
| F-058 | Purchase | Purchase accessory stock (resham / zari) | P_STOCK, P_PURJA | Not captured |
| F-060 | Sales | Customer order | ORDER, ORDERDETAIL | Not captured |
| F-061 | Sales | Packing slip | PSLIP, PSLIPDETAIL, PSLIPLINK, PSLIPSTOCKD, ORDERLINKPSLIP | Not captured |
| F-062 | Sales | Sale invoice | SALES (TRMD 4), STOCK, EXP_INV, INVDETAIL, ORDERLINK | Not captured |
| F-063 | Sales | Cash memo (retail) | SALES (TRMD 1), STOCK, CUSTOMERMASTER | Not captured |
| F-064 | Sales | Approval issue (jakad) | STOCKO, CSMASTAPP, RECPAYAPP | Not captured |
| F-065 | Sales | Approval sale | CSMASTAPPSALE, RECPAYAPPSALE | Not captured |
| F-066 | Sales | Approval return | STOCKI.QTY_R | Not captured |
| F-067 | Sales | Credit note / sales return | SALES (TRMD 5) | Not captured |
| F-068 | Sales | Debit note | SALES (TRMD 6) | Not captured |
| F-069 | Sales | Proforma invoice | (Proforma.rpt) | Not captured |
| F-070 | Sales | Despatch without order | (DespatchWithoutOrder.rpt) | Not captured |
| F-071 | Sales | Gate pass confirmation | (GPConfirm.rpt) | Not captured |
| F-072 | Sales | Transport details (LR / bilty) | TBLTERMSLINK, STOCK.LRNO, SALESJ.BILTY | Not captured |
| F-073 | Sales | Sale–purchase link | SALEPURLINK | Not captured |
| F-074 | Sales | Invoice detail / narration text | INVDETAIL, inword | Not captured |
| F-080 | Job Work | Job issue to karigar | STOCKJ, STOCKJD, STOCKO, STOCKJOB | Not captured |
| F-081 | Job Work | Job receive from karigar | STOCKC, STOCKJP, KARRCDLINKKARINV | Not captured |
| F-082 | Job Work | Karigar bill | SALESJ | Not captured |
| F-083 | Job Work | Karigar payment / advance | PAYMENTJ, PAYDETAILJ | Not captured |
| F-084 | Job Work | Karigar payment return | PAYMENTJR, PAYDETAILJR | Not captured |
| F-085 | Job Work | Party job challan / receive | STOCKPJOB, JOBDETAILPARTY | Not captured |
| F-086 | Job Work | Party job invoice | SALESPJOB, RECPAYJOB | Not captured |
| F-090 | Manufacturing | Raw material master | TBLRawMast, MMATERIAL | Not captured |
| F-091 | Manufacturing | Raw material opening / stock | TBLSTOCKORAW | Not captured |
| F-092 | Manufacturing | Formula (BOM) | MFORMULA | Not captured |
| F-093 | Manufacturing | Costing sheet (MRP master) | MRPMASTER, MRPDETAIL | Not captured |
| F-094 | Manufacturing | Raw material purchase / sale | TBLSALESRAW, TBLSTOCKRAW, TBLEXPTRANSRAW | Not captured |
| F-095 | Manufacturing | Material issue to department | TBLPISSU, TBLPISSUDETAIL, MLINK | Not captured |
| F-096 | Manufacturing | Production / manufacturing entry | MSALES, MSTOCK | Not captured |
| F-097 | Manufacturing | Transaction description lines | TBLTRANSACTION | Not captured |
| F-100 | Accounts | Receipt / payment voucher | RECPAY, RECPAYDETAIL | Not captured |
| F-101 | Accounts | Journal / contra | RECPAY | Not captured |
| F-102 | Accounts | Cash book (daily cash) | CSMAST | Not captured |
| F-103 | Accounts | Petty cash (peti) | CSMASTPETI, RECPAYPETI | Not captured |
| F-104 | Accounts | Bill-wise receipt adjustment | RECEIPT | Not captured |
| F-105 | Accounts | Cheque issue / payment register | PAYMENT, MCHEQUE | Not captured |
| F-106 | Accounts | Supplier payment | PAYMENTSUP, PAYDETAILSUP | Not captured |
| F-107 | Accounts | TDS entry | TDS | Not captured |
| F-108 | Accounts | Bank reconciliation | RECPAY, PAYMENT.DATE_I | Not captured |
| F-109 | Accounts | Expense voucher (other expenses) | EXP, EXPTRANS | Not captured |
| F-110 | Accounts | Schedule heads | SCHHEAD | Not captured |
| F-111 | Accounts | Cheque received / post-dated register entry | PAYMENT, RECPAY (cheque fields) | Not captured |
| F-112 | Accounts | Party reconciliation / ledger confirmation | (PartyReco.rpt, LedgerConf.rpt) | Not captured |
| F-120 | Stock | Opening stock | SAREEOPEN, SAREE.OP_ST | Not captured |
| F-121 | Stock | Stock in / out / adjustment | STOCKI, STOCKO | Not captured |
| F-122 | Stock | Saree labels / barcode | SAREELABEL | Not captured |
| F-123 | Stock | Photo catalogue | STOCKPIC, iimage | Not captured |
| F-124 | Stock | Stock transfer between companies (central item master) | SilkSol.ini ItemMaster Central / MasterComp | Not captured |
| F-130 | CRM & SMS | Customer master | CUSTOMERMASTER | Not captured |
| F-131 | CRM & SMS | Customer group | CUSTGRPMASTER | Not captured |
| F-132 | CRM & SMS | Family members | FAMILYMEMBER | Not captured |
| F-133 | CRM & SMS | Address book contacts | ADDMASTER, ADDGRPMASTER, ADDFAMILYMEMBER | Not captured |
| F-134 | CRM & SMS | SMS send / templates | SMS, SALES.SMSSEND | Not captured |
| F-135 | CRM & SMS | SMS log | — | New |
| F-140 | GST | GSTR-1 / GSTR-3B / HSN / e-invoice / e-way bill (new) | — | New |
| F-141 | GST | Enter IRN / ack / e-way bill number for a document | — | New |
| F-142 | GST | Mark GST period filed | — | New |
| F-150 | Utilities | Backup / restore | SilkSol.ini BackupPath, BackupOption | Not captured |
| F-151 | Utilities | Check database / rebuild | Setup.ini CheckDataBase | Not captured |
| F-152 | Utilities | Background photo / resolution settings | SilkSol.ini Background, Resol | Not captured |
| F-153 | Utilities | Report path setting | Setup.ini ReportPath | Not captured |
| F-154 | Utilities | Sale barcode setting | Setup.ini SaleBarcode | Not captured |
| F-155 | Utilities | Printer / text printing | PRNTEXT.BAT | Not captured |
| F-156 | Utilities | Settings (all company settings in one screen) | settings | New |
| F-157 | Utilities | Re-post documents | — | New |
| F-158 | Utilities | Import masters from CSV | — | New |
| F-159 | Utilities | Audit log viewer | RECPAYDETAIL (user / mode / date / time) | New |

## Printed formats and reports

Generated from the file names in `C:\SilkSol\Report` and `C:\SilkSol\ChequeBook`. Only the names
were read; the `.rpt` files are not opened or reused. Layouts are rebuilt from sample prints.

| ID | Group | SilkSol format name | Status |
|----|-------|---------------------|--------|
| R-001 | Stationery | AddBook | Not captured |
| R-002 | Stationery | AddBook1 | Not captured |
| R-003 | Stationery | AddBook2 | Not captured |
| R-004 | Stationery | AddBookCombined | Not captured |
| R-005 | Stationery | AddBookDetail (1) | Not captured |
| R-006 | Stationery | AddBookDetail | Not captured |
| R-007 | Stationery | AddBookDetailCombined | Not captured |
| R-008 | Stationery | AddBookDetailPersonal | Not captured |
| R-009 | Stationery | AddBookPersonal | Not captured |
| R-010 | Stationery | AddLabel | Not captured |
| R-011 | Stationery | AddressBook1 | Not captured |
| R-012 | Documents | AgentCopy | Not captured |
| R-013 | Documents | AGENTCOPY1 | Not captured |
| R-014 | Stationery | AgentLabel | Not captured |
| R-015 | Documents | AngadiaCopy | Not captured |
| R-016 | Documents | AngadiaCopyLess | Not captured |
| R-017 | Documents | AngadiaRegister | Not captured |
| R-018 | Documents | AppPurja | Not captured |
| R-019 | Documents | AppPurjaWR | Not captured |
| R-020 | Documents | Apprtd | Not captured |
| R-021 | Documents | ApprtdWR | Not captured |
| R-022 | Documents | APPSALE | Not captured |
| R-023 | Documents | APPSaleRtd | Not captured |
| R-024 | Accounts | BalsSheet | Not captured |
| R-025 | Accounts | BankSlip | Not captured |
| R-026 | Stock / labels | BarCode | Not captured |
| R-027 | Stock / labels | BarCodeRetail | Not captured |
| R-028 | Documents | BillReport | Not captured |
| R-029 | Documents | BillReportC | Not captured |
| R-030 | Documents | BiltyReport | Not captured |
| R-031 | Accounts | CASHBOOK | Not captured |
| R-032 | Accounts | CASHBOOK1 | Not captured |
| R-033 | Accounts | CASHBOOK3 | Not captured |
| R-034 | Accounts | CashBookCont | Not captured |
| R-035 | Accounts | CashBookCont3 | Not captured |
| R-036 | Documents | Cashmemo | Not captured |
| R-037 | Documents | Cashmemoagentcopy | Not captured |
| R-038 | Documents | Cashmemoangadiacopy | Not captured |
| R-039 | Documents | Cashmemoofficecopy | Not captured |
| R-040 | Documents | Cashmemopslipcopy | Not captured |
| R-041 | Stock / labels | Catelog | Not captured |
| R-042 | Stock / labels | Catelog1 | Not captured |
| R-043 | Stock / labels | Catelog2 | Not captured |
| R-044 | Documents | Certificate | Not captured |
| R-045 | Accounts | ChequeRegIssue | Not captured |
| R-046 | Accounts | ChequeRegPost | Not captured |
| R-047 | Accounts | ChequeRegPurja | Not captured |
| R-048 | Accounts | Counter | Not captured |
| R-049 | Documents | CRAGENTCOPY | Not captured |
| R-050 | Documents | CRANGADIACOPY | Not captured |
| R-051 | Documents | CRNOTE | Not captured |
| R-052 | Documents | CROFFICECOPY | Not captured |
| R-053 | Accounts | DayBook | Not captured |
| R-054 | Accounts | DayBookCont | Not captured |
| R-055 | Sales & orders | DespatchWithoutOrder | Not captured |
| R-056 | Documents | DRAGENTCOPY | Not captured |
| R-057 | Documents | DRANGADIACOPY | Not captured |
| R-058 | Documents | DRNOTE | Not captured |
| R-059 | Documents | DROFFICECOPY | Not captured |
| R-060 | Stationery | Envelope | Not captured |
| R-061 | Stationery | EnvelopeAdd | Not captured |
| R-062 | Stationery | EnvelopeAddPersonal | Not captured |
| R-063 | Stationery | EnvelopeAgent | Not captured |
| R-064 | Stationery | EnvelopePre | Not captured |
| R-065 | Stock | FabPur | Not captured |
| R-066 | Stock | FabricPurchaseRegister | Not captured |
| R-067 | Documents | GPConfirm | Not captured |
| R-068 | Graphs | Graph | Not captured |
| R-069 | Graphs | GraphReport | Not captured |
| R-070 | Stock / labels | InvPic | Not captured |
| R-071 | Job / manufacturing | ItemDebitNote | Not captured |
| R-072 | Job / manufacturing | ItemIssue | Not captured |
| R-073 | Accounts | ItemLedger | Not captured |
| R-074 | Accounts | ItemLedgerAgentCommission | Not captured |
| R-075 | Accounts | ItemLedgerJob | Not captured |
| R-076 | Accounts | ItemLedgerJob2 | Not captured |
| R-077 | Accounts | ItemLedgerJobParty | Not captured |
| R-078 | Accounts | ItemLedgerParty | Not captured |
| R-079 | Job / manufacturing | ItemReceive | Not captured |
| R-080 | Job / manufacturing | ItemSummaryJobParty | Not captured |
| R-081 | Documents | Jakad | Not captured |
| R-082 | Job / manufacturing | JobBarVch | Not captured |
| R-083 | Job / manufacturing | JobChallan | Not captured |
| R-084 | Job / manufacturing | JobDeliveryStatus | Not captured |
| R-085 | Job / manufacturing | JobInvoice | Not captured |
| R-086 | Job / manufacturing | JobIssueDetail | Not captured |
| R-087 | Job / manufacturing | JobPurPayment | Not captured |
| R-088 | Job / manufacturing | JobRecd | Not captured |
| R-089 | Job / manufacturing | JobReceiveDetail | Not captured |
| R-090 | Job / manufacturing | JobReceiveDetailParty | Not captured |
| R-091 | Job / manufacturing | JobSales | Not captured |
| R-092 | Job / manufacturing | JobVch | Not captured |
| R-093 | Accounts | Journal | Not captured |
| R-094 | Accounts | JournalCont | Not captured |
| R-095 | Job / manufacturing | KarigarHistory | Not captured |
| R-096 | Accounts | KarigarPaymentLedger | Not captured |
| R-097 | Accounts | Ledger | Not captured |
| R-098 | Accounts | LedgerCash | Not captured |
| R-099 | Accounts | LedgerConf | Not captured |
| R-100 | Accounts | LedgerCont | Not captured |
| R-101 | Accounts | LedgerDetail | Not captured |
| R-102 | Accounts | LedgerF2 | Not captured |
| R-103 | Accounts | LedgerMultiYear | Not captured |
| R-104 | Accounts | LedgerReco | Not captured |
| R-105 | Documents | LrNotFound | Not captured |
| R-106 | Documents | MbillReport | Not captured |
| R-107 | Job / manufacturing | mfgItemReceiveKar | Not captured |
| R-108 | Job / manufacturing | mfgMatIssue | Not captured |
| R-109 | Accounts | MONTHSUMM6 | Not captured |
| R-110 | Stock / labels | MRP | Not captured |
| R-111 | Documents | OfficeCopy | Not captured |
| R-112 | Sales & orders | Order | Not captured |
| R-113 | Sales & orders | OrderA4 | Not captured |
| R-114 | Sales & orders | OrderCat | Not captured |
| R-115 | Sales & orders | OrderFullsize | Not captured |
| R-116 | Sales & orders | OrderPic | Not captured |
| R-117 | Sales & orders | OrderReport | Not captured |
| R-118 | Sales & orders | OrderReportItemWise | Not captured |
| R-119 | Sales & orders | OrderReportPartyWise | Not captured |
| R-120 | Sales & orders | OrderReportPartyWise1 | Not captured |
| R-121 | Sales & orders | OrderReportPWise | Not captured |
| R-122 | Sales & orders | OrderReportSuppWise | Not captured |
| R-123 | Sales & orders | OrderReportSuppWiseItemOdr | Not captured |
| R-124 | Sales & orders | OrderReportSuppWisePartyOdr | Not captured |
| R-125 | Sales & orders | OrderSearch | Not captured |
| R-126 | Sales & orders | OrderWithoutRate | Not captured |
| R-127 | Accounts | Outs | Not captured |
| R-128 | Accounts | OutsBillWise | Not captured |
| R-129 | Accounts | OutsCont | Not captured |
| R-130 | Accounts | OutsMSummary | Not captured |
| R-131 | Accounts | OutsSummary | Not captured |
| R-132 | Accounts | OutsSummDetail | Not captured |
| R-133 | Documents | PackingSlip | Not captured |
| R-134 | Accounts | PartyReco | Not captured |
| R-135 | Accounts | PartyStatistics | Not captured |
| R-136 | Accounts | pay | Not captured |
| R-137 | Accounts | PaymentSearch | Not captured |
| R-138 | Stock / labels | PriceList | Not captured |
| R-139 | Accounts | ProfitLoss | Not captured |
| R-140 | Documents | Proforma | Not captured |
| R-141 | Documents | PSlip | Not captured |
| R-142 | Documents | PSlip1 | Not captured |
| R-143 | Documents | PSlip1104Full | Not captured |
| R-144 | Documents | PSlip1104half | Not captured |
| R-145 | Documents | PSlip2 | Not captured |
| R-146 | Documents | PSlip3 | Not captured |
| R-147 | Documents | PSLIPA4 | Not captured |
| R-148 | Documents | PSlipCat | Not captured |
| R-149 | Documents | PslipCopy | Not captured |
| R-150 | Documents | Pslipfullsize | Not captured |
| R-151 | Documents | PSlipgg | Not captured |
| R-152 | Documents | PSlipPic | Not captured |
| R-153 | Documents | Pslipreport | Not captured |
| R-154 | Documents | PslipSearch | Not captured |
| R-155 | Stock | PurchaseHistory | Not captured |
| R-156 | Stock | PurchaseNotIssue | Not captured |
| R-157 | Documents | Purja | Not captured |
| R-158 | Documents | PurjaA61 | Not captured |
| R-159 | Documents | PurjaNew | Not captured |
| R-160 | Documents | PurjaWN | Not captured |
| R-161 | Documents | SaleRegister | Not captured |
| R-162 | Documents | SaleSearch | Not captured |
| R-163 | Stock / labels | SareeBarcode | Not captured |
| R-164 | Stock / labels | SareeLabel | Not captured |
| R-165 | Stock / labels | SareeLabel1 | Not captured |
| R-166 | Stock / labels | SareeLabel2 | Not captured |
| R-167 | Stock / labels | SareeLabel5x65 | Not captured |
| R-168 | Stock / labels | SareeLabelWithInv | Not captured |
| R-169 | Stock | StockInHand | Not captured |
| R-170 | Stock | StockItemWise | Not captured |
| R-171 | Stock | StockLedg | Not captured |
| R-172 | Stock | StockRegister | Not captured |
| R-173 | Sales & orders | SupOrder | Not captured |
| R-174 | Sales & orders | SupOrderCat | Not captured |
| R-175 | Sales & orders | SupOrderPic | Not captured |
| R-176 | Accounts | Trial | Not captured |
| R-177 | Accounts | TrialDetail | Not captured |
| R-178 | Accounts | Voucher | Not captured |
| R-179 | Job / manufacturing | VoucherSearchJob | Not captured |
| R-180 | Cheque printing | 1HDFC | Not captured |
| R-181 | Cheque printing | 1HDFC-Quick | Not captured |
| R-182 | Cheque printing | Bank Of Baroda | Not captured |
| R-183 | Cheque printing | Bank Of India | Not captured |
| R-184 | Cheque printing | ICICI Bank | Not captured |
| R-185 | Cheque printing | IDBI Bank | Not captured |
| R-186 | Cheque printing | Kotak Mahindra Bank | Not captured |
| R-187 | Cheque printing | Oriental Bank of Commerce | Not captured |
| R-188 | Cheque printing | SBI | Not captured |
| R-189 | Cheque printing | Union Bank Of India | Not captured |
| R-190 | GST / new | GSTR-1 (B2B, B2CL, B2CS, CDNR, CDNUR, HSN, documents issued) | New |
| R-191 | GST / new | GSTR-3B summary | New |
| R-192 | GST / new | HSN summary | New |
| R-193 | GST / new | Purchase register with input tax | New |
| R-194 | GST / new | E-invoice JSON | New |
| R-195 | GST / new | E-way bill JSON | New |
| R-196 | Manufacturing / new | Raw material stock and reorder level | New |
