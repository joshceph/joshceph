# Invoice generator (Google Sheets)

Type a client name, get a copy-and-send invoice message with items, totals, down payment and balance due. No add-ons, no cost.

## Setup

1. Create a Google Sheet with two tabs named **Inventory** and **Invoice**.
2. Inventory tab, row 1 headers in columns A to Q:
   STATUS, BATCH, BRAND, CLIENT NAME, ORDER #, ITEM, QTY, COLOR, CODE, SIZE, FOREIGN PRICE, LOCAL PRICE, SERVICE CHARGE, CHECKOUT FEE, TOTAL PRICE, DOWN PAYMENT, BALANCE
3. Inventory row 2 formulas (copy them down for new rows):
   - L2 (local price): `=IF(K2="","",K2*<your exchange rate cell>)`
   - O2 (total price): `=IF(L2="","",L2*G2+N(M2)+N(N2))`
   - Q2 (balance): `=IF(O2="","",O2-N(P2))`
4. Invoice tab labels: A4 "Client name", D4 "Batch (optional)", A5 "Invoice date", D5 "Invoice no.", A6 "Payment details", A9 to A16 the summary labels shown below.
5. Put your payment details in B6. Pick the client in B4 (a dropdown from Inventory column D works well). Batch in E4 is optional.

## Invoice tab formulas

The filter used everywhere (client match, optional batch match):
`LET(n,LOWER(TRIM($B$4)),b,TO_TEXT($E$4),(LOWER(TRIM(Inventory!$D$2:$D))=n)*(((b="")+(TO_TEXT(Inventory!$B$2:$B)=b))>0))`

Call it FILTER_RULE below.

| Cell | Formula |
|---|---|
| B5 | `=TODAY()` |
| E5 | `=IF($B$4="","","INV-"&TEXT(TODAY(),"yymmdd")&"-"&UPPER(LEFT(SUBSTITUTE($B$4," ",""),4)))` |
| B9 orders | `=IF($B$4="","",IFERROR(COUNTUNIQUE(FILTER(Inventory!$E$2:$E,FILTER_RULE)),0))` |
| B10 items | `=IF($B$4="","",SUM(IFERROR(FILTER(IFERROR(Inventory!$G$2:$G*1,0),FILTER_RULE),0)))` |
| B11 items subtotal | `=IF($B$4="","",SUM(IFERROR(FILTER(IFERROR(Inventory!$L$2:$L*1,0)*IFERROR(Inventory!$G$2:$G*1,0),FILTER_RULE),0)))` |
| B12 service charges | same as B10 with column M (no quantity multiplier) |
| B13 checkout fees | same as B12 with column N |
| B14 total | same as B12 with column O |
| B15 down payment | same as B12 with column P |
| B16 balance due | `=IF($B$4="","",B14-B15)` |

Replace FILTER_RULE with the full LET(...) expression above in each cell.

Line items (A19):
`=IF($B$4="","",IFERROR(SORT(FILTER({Inventory!$E$2:$E,Inventory!$F$2:$F,Inventory!$H$2:$H,Inventory!$J$2:$J,IFERROR(Inventory!$G$2:$G*1,0),IFERROR(Inventory!$L$2:$L*1,0),IFERROR(Inventory!$M$2:$M*1,0)+IFERROR(Inventory!$N$2:$N*1,0),IFERROR(Inventory!$O$2:$O*1,0)},FILTER_RULE),1,TRUE),"No items found for this client"))`

## Copy-and-send message (J5)

Builds a message with greeting, invoice number, date, item list, orders, items, subtotal, optional service charge and checkout fee lines (shown only when above zero), total, optional "Less down payment" line, balance due, payment details and a thank-you. Join the pieces with `CHAR(10)` for line breaks, and use `TEXTJOIN(CHAR(10),TRUE,FILTER("- "&Inventory!$F$2:$F&" ("&Inventory!$H$2:$H&", size "&Inventory!$J$2:$J&") x"&Inventory!$G$2:$G&": "&TEXT(IFERROR(Inventory!$O$2:$O*1,0),"#,##0.00"),FILTER_RULE))` for the item lines.

## Tips

- Set the sheet time zone (File, Settings) so the invoice date is right.
- Keep client names consistent. Matching ignores capitals and extra spaces.
- Lock the Invoice tab formulas (Data, Protect sheets and ranges) and leave only B4, E4 and B6 editable.
