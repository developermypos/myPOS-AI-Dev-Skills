# REVERSAL

Initiates an automatic reversal of a previously authorized transaction. Can only reverse the **last** successfully authorized transaction that is still pending in the current batch.

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `REVERSAL` | M | |
| `SID` | UUID | M | New unique session ID for this reversal request |
| `SID_ORIGINAL` | UUID | M | SID from the original PURCHASE session to reverse |
| `PASSWORD` | `****` | O | Required in some configurations. Terminal returns `STATUS=51` if needed |
| `LANG` | `EN` | O | Alpha-2 ISO language code |

## Response — Stage 1 (Terminal Accepted)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `17`=transaction not found, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: acquiring host port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

## Response — Stage 4 (Slave Mode Data Proxy)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=communication error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data to proxy to acquiring host |

## Response — Stage 5 (Final Result)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=communication error, `17`=transaction not found, `100`=OK with additional info |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `APPROVAL` | C | Reversal result code. See special codes below |
| `TX_STATUS` | C | See Appendix III |
| `AUTH_CODE` | C | |
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
| `STAN` | C | |

## APPROVAL Special Codes for REVERSAL

| APPROVAL | Meaning | Caller Action |
|----------|---------|--------------|
| `00` | Reversed successfully | Mark reversal as success |
| `25` | Record not found | Mark as success — terminal did not find the original; no reversal needed |
| `40` | Reversal not supported by terminal | Stop attempts; reversal must be done via back-office only |
| `94` | Duplicate — already reversed | Mark as success; debit cardholder if this is the first time receiving this code |
| `05` | No reversal needed | Original transaction was not approved; nothing to reverse |
| `13` | Invalid amount | Partial reversal amount is not less than the original, is missing, or is zero |

**Note:** Other decline codes are possible for partial reversals (partial reversal must be approved by the card issuer).

## See Also

- references/cancel-tx.md (via references/complete-cancel-tx.md) — for automatic reversal of the last uncompleted transaction
- references/appendices.md — all status codes
