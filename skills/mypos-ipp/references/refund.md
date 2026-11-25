# REFUND

Initiates a refund for a previously completed transaction. Unlike REVERSAL (which only works on the last uncompleted transaction), REFUND works on any completed transaction by reference. Full and partial refunds are supported.

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `REFUND` | M | |
| `SID` | UUID | M | New unique session ID for this refund |
| `AMOUNT` | `12.56` | M | Refund amount. Decimal point must match currency exponent |
| `CURRENCY` | `978` | M | ISO numeric currency code |
| `REFERENCE` | `201310152565` | O | Reference number identifying the original transaction |
| `LANG` | `EN` | O | Alpha-2 ISO language code |
| `MOTO_TX` | `1` | O | `1` = Mail Order / Telephone Order — manual card entry |
| `PAN` | `4111111111111111` | O | Card number. Conditional if MOTO_TX=1 |
| `EXP_DATE` | `1225` | O | Card expiry MMYY. Conditional if MOTO_TX=1 |
| `PRINT_RECEIPT` | `0` | O | Receipt printing mode. See Appendix VII |
| `PASSWORD` | `****` | O | Required in some configurations. Terminal returns `STATUS=51` if needed |

## Response — Stage 1 (Terminal Accepted)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update, `36`=deactivation not finished, `38`=unsupported version, `49`=wrong password, `50`=transaction forbidden, `51`=need password |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: acquiring host port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

## Response — Stage 2 (Card Waiting)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `5`=no card, `6`=unsupported card, `7`=chip error, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `2` |
| `TIMEOUT` | O | |

## Response — Stage 3 (PIN Entry)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `9`=invalid PIN, `10`=max PIN exceeded, `11`=PIN check online, `13`=user cancel, `14`=internal error |
| `STAGE` | M | `3` |
| `TIMEOUT` | O | |
| `TRIES_LEFT` | C | Remaining PIN attempts. Absent if STATUS=0, 11, or 13 |

## Response — Stage 4 (Slave Mode Data Proxy)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data to proxy to acquiring host |

## Response — Stage 5 (Final Result)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `17`=tx not found, `100`=OK with additional info |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `APPROVAL` | C | `00`=success. Any other value or empty = declined |
| `TX_STATUS` | C | See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number (12 digits) |
| `AMOUNT` | C | Refunded amount |
| `CURRENCY` | C | Currency (ISO numeric) |
| `CURRENCY_NAME` | C | Currency (ISO alpha) |
| `TERMINAL_ID` | C | Terminal ID |
| `MERCHANT_ID` | C | Merchant ID |
| `MERCHANT_NAME` | C | Merchant name |
| `MERCHANT_AL1` | C | Merchant address line 1 |
| `MERCHANT_AL2` | C | Merchant address line 2 |
| `PAN_MASKED` | C | Masked card number |
| `EMBOSS_NAME` | C | Cardholder name |
| `AID` | C | Application Identifier from chip |
| `AID_NAME` | C | AID name |
| `APPL_PREF_NAME` | C | Application preferred name |
| `STAN` | C | System Trace Audit Number |
| `SIGNATURE_NOT_REQ` | C | Obsolete. Use CVM |
| `CLIENT_TOKEN` | M | 40-byte ASCII hash of merchant+card |
| `DCC_CURRENCY` | C | DCC currency (ISO numeric) |
| `DCC_CURRENCY_NAME` | C | DCC currency (ISO alpha) |
| `DCC_AMOUNT` | C | Amount in card currency |
| `DCC_EXCHANGE_RATE` | C | Exchange rate |
| `CUSTOM_ROW1` | C | Receipt footer row 1 |
| `CUSTOM_ROW2` | C | Receipt footer row 2 |
| `CVM` | M | `P`=PIN, `S`=signature, `N`=none |
| `ENTRY_MODE` | M | `C`=chip, `S`=swipe, `M`=manual, `P`=contactless |
| `LOGO_TYPE` | M | `1`=none, `2`=default, `3`=custom |
| `LOGO_GUID` | C | Custom logo GUID (if LOGO_TYPE=3) |
| `PRINTER` | C | `0`=no printer, `1`=printer available |
| `DECLINED_REASON1` | C | Decline reason line 1 (if APPROVAL≠00) |
| `DECLINED_REASON2` | C | Decline reason line 2 (if APPROVAL≠00) |

## Notification Stages 11–14

Optional notification stages — same fields as documented in references/purchase.md.

## After REFUND — Required Follow-up

Call `COMPLETE_TX` with `SID_ORIGINAL` pointing to this refund session's SID. Otherwise the terminal returns `STATUS=20` on the next financial request.

## See Also

- references/complete-cancel-tx.md — COMPLETE_TX (required after refund)
- references/reversal.md — REVERSAL (same-batch transaction reversal)
- references/appendices.md
