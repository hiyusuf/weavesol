# Glossary — WeaveSol

Trade words used in screens, code and documents. Meanings marked _(confirm)_ are checked with the
owner in Phase 0.

| Term | Meaning | In WeaveSol |
|------|---------|-------------|
| **Purja** | Purchase bill / memo from a weaver or supplier | Doc type `PURJA` |
| **Karigar** | Artisan who does job work (polish, embroidery, dyeing, finishing) | `karigars` |
| **Job** | A kind of work given to a karigar (Polish, Embroidery, Hand Dye…) | `jobs` |
| **Angadia** | Courier / carrier service for parcels (and documents) | `transporters.kind = ANGADIA` |
| **Bilty / LR** | Lorry receipt from the transporter for a despatch | `document_transport.lr_no` |
| **Jakad** | Goods sent to a party on approval _(confirm)_ | Doc types `APP_ISSUE` / `APP_RETURN` |
| **Than** | A roll / length of fabric, counted in metres | Item type "Than", unit Mtrs. |
| **Peti** | Petty cash _(confirm)_ | Doc type `PETTY`, petty cash book |
| **Packing slip (P-Slip)** | List of pieces / bales packed for a party | Doc type `PSLIP` |
| **Bale** | A packed bundle in a despatch, with metres and pieces | `document_line_bales` |
| **Stock no. (ST_NO)** | Number given to each piece, printed on its label / barcode | `stock_pieces.stock_no` |
| **Series** | Separate invoice number run (e.g. "Main") | `number_series` |
| **Bill charges / expenses** | The 9 add / less amounts on a bill (commission, polish, packing, freight…) | `bill_charges` |
| **Less %** | Standard deduction % for a party | `ledgers.less_percent` |
| **Office / Agent / Angadia copy** | Copies of the same invoice for different holders | `print_formats` |
| **Gate pass** | Permission slip for goods leaving the premises | Gate pass confirmation report |
| **Rate code** | Coded cost price printed on labels | `items.rate_code` |
| **Warp / Weft / Reed / Pick** | Weaving specifications of a fabric | `items` columns |
| **Place of supply** | State that decides CGST+SGST or IGST | `documents.place_of_supply_state_id` |
| **HSN / SAC** | GST goods / services classification code | `hsn_codes` |
| **UQC** | GST unit code (PCS, MTR, KGS) | `units.uqc` |
| **IRN** | E-invoice reference number from the GST portal | `document_gst_einvoice.irn` |
| **EWB** | E-way bill number | `document_gst_einvoice.ewb_no` |
| **DLT** | Indian SMS template registration (TRAI) | `sms_templates.dlt_template_id` |
| **TRMD** | SilkSol's internal document-type code (reference only) | `doc_types.silksol_trmd` |
