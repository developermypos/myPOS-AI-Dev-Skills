# CASH_ADVANCE

Cash advance transactions allow a cardholder to withdraw cash at the point of sale. **Available only on myPOS Retail 80 (model Q80).** Supports EMV chip cards only — contactless and magnetic stripe are not accepted for cash advance.

---

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `CASH_ADVANCE` | M | |
| `SID` | UUID | M | |
| `AMOUNT` | `100.00` | M | Cash advance amount |
| `CURRENCY` | `978` | M | ISO numeric currency code |
| `LANG` | `EN` | O | |
| `REFERENCE_NUMBER_TYPE` | `1` | O | `1`=reference number, `2`=invoice number, `3`=product number, `4`=reservation number |
| `REFERENCE_NUMBER` | `INV-123` | C | Required when `REFERENCE_NUMBER_TYPE` is set |
| `PRINT_RECEIPT` | `0` | O | Receipt behavior. See Appendix VII: `0`=merchant+customer copy, `1`=customer confirmation, `2`=merchant only, `3`=no print |

---

## Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

---

## Response — Stage 2 (Card Interaction)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=card waiting, `5`=no card found, `6`=unsupported card, `7`=card chip error, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `2` |
| `TIMEOUT` | O | |

---

## Response — Stage 3 (PIN Entry)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=PIN check online, `9`=invalid PIN, `10`=max PIN count exceeded, `11`=PIN check online (entry complete), `13`=user cancel, `14`=internal error |
| `STAGE` | M | `3` |
| `TIMEOUT` | O | |
| `TRIES_LEFT` | C | Remaining PIN attempts. Present if STATUS≠0, ≠11, ≠13 |

---

## Response — Stage 4 (Slave Mode Data Proxy)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data to proxy to acquiring host. **Forward unmodified — do NOT add headers or framing.** |

Stage 4 may repeat multiple times in Slave mode. Continue proxying DATA until STAGE=5 arrives.

---

## Response — Stage 5 (Final Result)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `100`=OK with additional info (call GET_STATUS) |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `APPROVAL` | C | `00`=success, other=declined. Empty=not attempted (treat as declined) |
| `DECLINED_REASON1` | C | Primary decline reason code. See Appendix IX |
| `DECLINED_REASON2` | C | Secondary decline reason code |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number (12 digits) |
| `AMOUNT` | C | Cash advance amount |
| `CURRENCY` | C | ISO numeric currency code |
| `CURRENCY_NAME` | C | ISO alphabetic currency code |
| `TERMINAL_ID` | C | Terminal ID |
| `MERCHANT_ID` | C | Merchant ID |
| `MERCHANT_NAME` | C | Merchant name |
| `MERCHANT_AL1` | C | Merchant address line 1 |
| `MERCHANT_AL2` | C | Merchant address line 2 |
| `PAN_MASKED` | C | Masked card number |
| `EMBOSS_NAME` | C | Cardholder name |
| `AID` | C | Application Identifier. See Appendix IV |
| `AID_NAME` | C | AID name |
| `APPL_PREF_NAME` | C | Application preferred name |
| `STAN` | C | System Trace Audit Number |
| `SIGNATURE_NOT_REQ` | C | Obsolete. Use CVM |
| `PRINTER` | C | `0`=no printer, `1`=available |
| `DCC_CURRENCY` | C | DCC card currency (ISO numeric) |
| `DCC_CURRENCY_NAME` | C | DCC card currency (ISO alpha) |
| `DCC_AMOUNT` | C | Amount in card currency |
| `DCC_EXCHANGE_RATE` | C | Exchange rate |
| `CUSTOM_ROW1` | C | Custom receipt row 1 |
| `CUSTOM_ROW2` | C | Custom receipt row 2 |
| `CVM` | M | Cardholder verification: `P`=PIN, `S`=signature, `N`=none |
| `ENTRY_MODE` | M | Card entry: `C`=chip (only value for CASH_ADVANCE) |
| `REFERENCE_NUMBER_TYPE` | CE | Echoed if provided in request |
| `REFERENCE_NUMBER` | CE | Echoed if provided in request |
| `LOGO_TYPE` | M | `1`=none, `2`=myPOS default, `3`=custom |
| `LOGO_GUID` | C | Custom logo GUID (when LOGO_TYPE=3) |

## Notification Stages (Optional)

| Stage | Name | Properties |
|-------|------|-----------|
| `11` | USER_PRESENT_CARD | STATUS=0 — card has been presented |
| `12` | USER_PRESENT_DCC | STATUS=0 + DCC fields (if DCC offered) |
| `13` | USER_PRESENT_PIN | STATUS=0, TRIES_LEFT(C) |
| `14` | USER_SELECTED_DCC | STATUS=0/13/14, DCC_USED |

## Cancel In-Progress

With terminal software ≥ 3.45, CASH_ADVANCE can be cancelled during execution using CANCEL_STAGE. See `references/vending.md` — set `METHOD=CASH_ADVANCE` and `STAGE=101`.

## See Also

- references/complete-cancel-tx.md — COMPLETE_TX is NOT required after CASH_ADVANCE (no settlement procedure)
- references/vending.md — CANCEL_STAGE (to abort an in-progress CASH_ADVANCE)
- references/appendices.md — Appendix III (TX_STATUS), Appendix IV (AIDs), Appendix IX (decline reasons)
