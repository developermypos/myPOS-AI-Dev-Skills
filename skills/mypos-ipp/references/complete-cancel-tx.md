# COMPLETE_TX and CANCEL_TX

## COMPLETE_TX

Initiates the settlement and receipt procedure for a completed transaction. **Every successfully authorized PURCHASE, REFUND, or VENDING_PURCHASE must be completed with COMPLETE_TX** before a new financial transaction can be started. If a new transaction is requested without completing the previous one, the terminal returns `STATUS=20` (NOT COMPLETED LAST TX).

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `COMPLETE_TX` | M | |
| `SID` | UUID | M | New unique session ID |
| `SID_ORIGINAL` | UUID | M | SID from the transaction being completed |
| `RECEIPT_TYPE` | `2` | O | E-receipt delivery method. See Appendix V: `1`=no e-receipt, `2`=email, `3`=SMS |
| `CREDENTIAL` | `user@example.com` | C | Customer email or phone for e-receipt. Empty if RECEIPT_TYPE=1 |
| `AMOUNT` | `12.56` | C | **Required only for VENDING_PURCHASE** — the actual charged amount |
| `CURRENCY` | `978` | C | **Required only for VENDING_PURCHASE** |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

Stage 4 logic is the same as in PURCHASE. Proxy DATA bytes to the acquiring host unmodified.

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data to proxy to acquiring host |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `17`=tx not found, `100`=OK with additional info |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## CANCEL_TX

Initiates an automatic reversal of the **last successfully authorized transaction that has not yet been completed** with COMPLETE_TX. Only the most recent uncompleted transaction can be cancelled this way.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `CANCEL_TX` | M | |
| `SID` | UUID | M | New unique session ID |
| `SID_ORIGINAL` | UUID | M | SID of the transaction to cancel. Only the last successful uncompleted transaction can be cancelled |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `17`=tx not found, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `16`=SSL error |
| `STAGE` | M | `4` |
| `DATA` | C | Binary data to proxy to acquiring host |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `16`=SSL error, `19`=invalid amount |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `REFERENCE` | C | |
| `TX_STATUS` | C | See Appendix III |
| `AUTH_CODE` | C | |
| `APPROVAL` | C | `00`=reversed successfully |
| `TX_DATE_LOCAL` | C | |
| `TX_TIME_LOCAL` | C | |
| `RRN` | C | |
| `AMOUNT` | C | |
| `CURRENCY` | C | |
| `TERMINAL_ID` | C | |
| `MERCHANT_ID` | C | |
| `MERCHANT_NAME` | C | |
| `MERCHANT_AL1` | C | |
| `MERCHANT_AL2` | C | |
| `PAN_MASKED` | C | |
| `EMBOSS_NAME` | C | |
| `AID` | C | |
| `AID_NAME` | C | |
| `APPL_PREF_NAME` | C | |
| `STAN` | C | |
| `SIGNATURE_NOT_REQ` | C | Obsolete |
| `PRINTER` | C | `0`/`1` |

## See Also

- references/purchase.md — PURCHASE (COMPLETE_TX is required after every PURCHASE)
- references/refund.md — REFUND (COMPLETE_TX is also required after REFUND)
- references/vending.md — VENDING_PURCHASE (COMPLETE_TX requires AMOUNT+CURRENCY)
- references/reversal.md — REVERSAL (for reversing completed batch transactions)
- references/appendices.md
