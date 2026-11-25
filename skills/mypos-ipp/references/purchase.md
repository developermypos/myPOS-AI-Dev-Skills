# PURCHASE

Initiates a card payment. The terminal accepts the card, processes PIN entry, communicates with the acquiring host, and returns the authorization result.

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `PURCHASE` | M | |
| `SID` | UUID | M | Unique session ID from caller |
| `AMOUNT` | `12.56` | M | Positive amount. Decimal point position must match currency exponent (e.g., `10` is invalid for EUR; use `10.00`) |
| `CURRENCY` | `978` | M | ISO numeric currency code (978=EUR, 840=USD, etc.) |
| `LANG` | `EN` | O | Alpha-2 ISO language code. Default: EN. See Appendix VIII |
| `FIXED_PINPAD` | `1` | O | `1` = PIN pad digits in fixed order. Default: random order |
| `PREAUTH_TX` | `1` | O | `1` = pre-authorization mode (hold funds, complete later) |
| `MOTO_TX` | `1` | O | `1` = Mail Order / Telephone Order — manual card entry |
| `PAN` | `4111111111111111` | C | Card number. Required if `MOTO_TX=1` |
| `EXP_DATE` | `1225` | C | Card expiry in MMYY format. Required if `MOTO_TX=1` |
| `TIP_AMOUNT` | `2.00` | O | Tip amount to add to transaction |
| `OPERATOR_CODE` | `cashier01` | O | Operator or cashier identifier |
| `REFERENCE_NUMBER_TYPE` | `1` | O | Type of reference data: `1`=reference number, `2`=invoice number, `3`=product ID, `4`=reservation ID |
| `REFERENCE_NUMBER` | `TRN329149RFN` | C | Reference data value. Must be used together with `REFERENCE_NUMBER_TYPE` |
| `EXTERNAL_PROCESSING` | `1` | O | `1` = caller will handle the acquiring processing (Slave-extended). Returns TRACK1_DATA and TRACK2_DATA in stage 5 |
| `PRINT_RECEIPT` | `0` | O | Receipt printing mode. See Appendix VII |
| `PASSWORD` | `****` | C | Required for MOTO_TX in some configurations. Also required when terminal returns `STATUS=51` (NEED_PASSWORD) |

## Response — Stage 1 (Terminal Accepted Request)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update required, `36`=deactivation not finished, `38`=unsupported protocol version, `49`=wrong password, `50`=transaction forbidden. See Appendix I |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | Seconds until next message |
| `IP` | C | Slave mode: acquiring host hostname or IP |
| `PORT` | C | Slave mode: acquiring host port |
| `CHAIN` | C | Slave mode: up to 4 SSL fingerprints (20 bytes each, `;` separated) |

## Response — Stage 2 (Card Waiting)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK (waiting), `5`=no card found, `6`=unsupported card, `7`=card chip error, `13`=user cancel, `14`=internal error, `15`=communication error |
| `STAGE` | M | `2` |
| `TIMEOUT` | O | |

## Response — Stage 3 (PIN Entry)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `9`=invalid PIN, `10`=max PIN count exceeded, `11`=PIN check online, `13`=user cancel, `14`=internal error |
| `STAGE` | M | `3` |
| `TIMEOUT` | O | |
| `TRIES_LEFT` | C | Remaining PIN attempts. Absent if STATUS=0, 11, or 13 |

## Response — Stage 4 (Slave Mode Data Proxy)

Applicable only in Slave mode.

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=communication error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data blob to forward to acquiring host. Absent if STATUS≠0 or IP/PORT=-1. The 2-byte big-endian prefix specifies the length |

**Slave mode action for STAGE=4:** Send the DATA bytes verbatim to the acquiring host (IP:PORT from STAGE=1), receive the response, send the response bytes back to the terminal in a new STAGE=4 message. Repeat until STAGE=5.

## Response — Stage 5 (Final Result)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=communication error, `17`=transaction not found, `100`=OK with additional info (call GET_STATUS) |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` (final) |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code from issuer. Alpha-numeric (6). Present only on successful authorization |
| `APPROVAL` | C | Approval code from issuer. `00`=approved. Any other value or empty=declined/error. **APPROVAL=00 is the only success.** |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number. Numeric (12) |
| `AMOUNT` | C | Authorized amount |
| `CURRENCY` | C | ISO numeric currency code |
| `CURRENCY_NAME` | C | ISO alphabetic currency code (e.g., EUR) |
| `TERMINAL_ID` | C | Terminal ID. Alpha-numeric (8) |
| `MERCHANT_ID` | C | Merchant ID. Alpha-numeric (15) |
| `MERCHANT_NAME` | C | Merchant name. Alpha-numeric (22) |
| `MERCHANT_AL1` | C | Merchant address line 1. Alpha-numeric (40) |
| `MERCHANT_AL2` | C | Merchant address line 2. Alpha-numeric (40) |
| `PAN_MASKED` | C | Masked card number: `**** XXXX` (4 stars + space + last 4 digits) |
| `EMBOSS_NAME` | C | Cardholder name (where available) |
| `AID` | C | Application Identifier from chip card. See Appendix IV |
| `AID_NAME` | C | AID name (e.g., `Visa V PAY`) |
| `APPL_PREF_NAME` | C | Application preferred name from chip/contactless card |
| `STAN` | C | System Trace Audit Number. Numeric (6), range 000001–999999. Unique when combined with date |
| `SIGNATURE_NOT_REQ` | C | *Obsolete.* Use CVM instead. `1`=no signature, `0`=signature required |
| `CLIENT_TOKEN` | M | 40-byte ASCII hash of (merchant unique ID + card PAN). Fixed size, no length prefix |
| `DCC_CURRENCY` | C | DCC card currency. ISO numeric. Present only when DCC was chosen |
| `DCC_CURRENCY_NAME` | C | DCC card currency. ISO alphabetic |
| `DCC_AMOUNT` | C | Authorized amount in card's currency |
| `DCC_EXCHANGE_RATE` | C | Exchange rate between CURRENCY and DCC_CURRENCY |
| `CUSTOM_ROW1` | C | Optional text printed in receipt footer — row 1 |
| `CUSTOM_ROW2` | C | Optional text printed in receipt footer — row 2 |
| `CVM` | M | Cardholder Verification Method: `P`=verified PIN, `S`=signature required, `N`=no verification |
| `ENTRY_MODE` | M | How card was presented: `C`=chip, `S`=magstripe, `M`=manual key entry (MOTO), `P`=contactless |
| `LOGO_TYPE` | M | Logo in receipt header: `1`=none, `2`=default (myPOS), `3`=custom |
| `LOGO_GUID` | C | Custom logo GUID. Present only if LOGO_TYPE=3 |
| `TRACK1_DATA` | C | Track 1 card data. Present only if EXTERNAL_PROCESSING=1 |
| `TRACK2_DATA` | C | Track 2 card data. Present only if EXTERNAL_PROCESSING=1 |
| `PREAUTH_CODE` | C | Pre-authorization code. Present only if PREAUTH_TX=1. Required for COMPLETE_PREAUTH and CANCEL_PREAUTH |
| `PRINTER` | C | `0`=no printer, `1`=printer available |
| `DECLINED_REASON1` | C | First line of decline reason. Present when APPROVAL≠00. See Appendix IX |
| `DECLINED_REASON2` | C | Second line of decline reason. Present when APPROVAL≠00 |

## Notification Stages (Optional, 11–14)

These may arrive before STAGE=5. Handle or ignore as needed.

### Stage 11 — USER_PRESENT_CARD

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0` |
| `STAGE` | M | `11` |

### Stage 12 — USER_PRESENT_DCC (DCC Screen Shown)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0` |
| `STAGE` | M | `12` |
| `AMOUNT` | M | Transaction amount in POS currency |
| `CURRENCY` | M | POS currency (ISO numeric) |
| `CURRENCY_NAME` | M | POS currency (ISO alpha) |
| `DCC_CURRENCY` | M | Card currency (ISO numeric) |
| `DCC_CURRENCY_NAME` | M | Card currency (ISO alpha) |
| `DCC_AMOUNT` | M | Amount in card currency |
| `DCC_EXCHANGE_RATE` | M | Exchange rate (up to 8 chars including decimal) |

### Stage 13 — USER_PRESENT_PIN

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0` |
| `STAGE` | M | `13` |
| `TRIES_LEFT` | C | Remaining PIN attempts (if offline PIN wrong) |

### Stage 14 — USER_SELECTED_DCC

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`, `13`, or `14` |
| `STAGE` | M | `14` |
| `DCC_USED` | M | `1`=customer chose DCC. Sent only after STAGE=12 |

## After PURCHASE — Required Follow-up

After receiving STAGE=5 from PURCHASE:
1. Check `APPROVAL=00` before delivering goods/services.
2. Call `COMPLETE_TX` with `SID_ORIGINAL` set to this session's SID. For VENDING_PURCHASE, include AMOUNT and CURRENCY.
3. If `PREAUTH_TX=1`, save the `PREAUTH_CODE` — required later for COMPLETE_PREAUTH or CANCEL_PREAUTH.

## See Also

- references/complete-cancel-tx.md — COMPLETE_TX (required after every purchase)
- references/preauth.md — COMPLETE_PREAUTH / CANCEL_PREAUTH
- references/get-status.md — STATUS=100 follow-up
- references/appendices.md — all status codes
