# GET_STATUS

Used to:
1. **Check terminal readiness** — poll whether the terminal is idle and ready (STAGE=1 response)
2. **Retrieve the last transaction result** — get all fields from the most recently executed payment (STAGE=5 response)

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `GET_STATUS` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

## Response — Stage 1 (Terminal Readiness Check)

Returns when the terminal is idle and checking status only (no last-transaction data requested).

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `CURRENT_VERSION` | M | Current protocol version running on the device |

## Response — Stage 5 (Last Transaction Data)

Returns when called after a completed transaction or when STATUS=100 was received from a previous operation.

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `20`=not completed last tx, `25`=activation required, `26`=mandatory update, `34`=optional update, `36`=deactivation not finished |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `SID_ORIGINAL` | C | SID of the last executed transaction session |
| `REFERENCE` | O | Reference number from last transaction |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `APPROVAL` | C | `00`=success, other=declined |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number (12 digits) |
| `AMOUNT` | C | Transaction amount |
| `CURRENCY` | C | ISO numeric currency code |
| `CURRENCY_NAME` | C | ISO alphabetic currency code |
| `TERMINAL_ID` | C | Terminal ID |
| `MERCHANT_ID` | C | Merchant ID |
| `MERCHANT_NAME` | C | Merchant name |
| `MERCHANT_AL1` | C | Merchant address line 1 |
| `MERCHANT_AL2` | C | Merchant address line 2 |
| `PAN_MASKED` | C | Masked card number (`**** XXXX`) |
| `EMBOSS_NAME` | C | Cardholder name |
| `AID` | C | Application Identifier from chip. See Appendix IV |
| `AID_NAME` | C | AID name |
| `APPL_PREF_NAME` | C | Application preferred name |
| `STAN` | C | System Trace Audit Number |
| `SIGNATURE_NOT_REQ` | C | Obsolete. See CVM |
| `CLIENT_TOKEN` | M | 40-byte ASCII hash of merchant+card |
| `PRINTER` | C | `0`=no printer, `1`=available |
| `PRIMARY_CHAIN` | C | Slave mode: primary certificate chain fingerprints (up to 4 × 20 bytes, `;` separated) |
| `SECONDARY_CHAIN` | C | Slave mode: secondary certificate chain fingerprints (used for UPDATE method) |
| `LAST_TX_TYPE` | C | Type of last transaction: `00`=PURCHASE, `20`=REFUND, `02`=REVERSAL |
| `PREAUTH_CODE` | C | Pre-authorization code. Present if last PURCHASE had `PREAUTH_TX=1` |
| `TRACK1_DATA` | C | Track 1 data. Present if last PURCHASE had `EXTERNAL_PROCESSING=1` |
| `TRACK2_DATA` | C | Track 2 data. Same condition as TRACK1_DATA |
| `DCC_CURRENCY` | C | DCC card currency (ISO numeric) |
| `DCC_CURRENCY_NAME` | C | DCC card currency (ISO alpha) |
| `DCC_AMOUNT` | C | Amount in card currency |
| `DCC_EXCHANGE_RATE` | C | Exchange rate |

## Common Use Cases

**After STATUS=100 on any method:** Immediately call GET_STATUS to retrieve the supplementary transaction details.

**Polling terminal readiness:** Call GET_STATUS and check STAGE=1 + STATUS=0 before sending a new transaction.

**Recovering after disconnect:** Call GET_STATUS to determine whether the last transaction succeeded before deciding whether to retry.

## See Also

- references/complete-cancel-tx.md — COMPLETE_TX (follow GET_STATUS result if transaction completed)
- references/appendices.md — status codes
