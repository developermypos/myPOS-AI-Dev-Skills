# COMPLETE_PREAUTH and CANCEL_PREAUTH

Pre-authorization (initiated with `PURCHASE` using `PREAUTH_TX=1`) holds funds on the cardholder's account. Once the final amount is known, call `COMPLETE_PREAUTH` to capture the funds or `CANCEL_PREAUTH` to release the hold. The `PREAUTH_CODE` from the original PURCHASE STAGE=5 response is required for both.

## COMPLETE_PREAUTH

Finalizes a pre-authorization by charging the actual amount (must be ≤ the original pre-auth amount).

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `COMPLETE_PREAUTH` | M | |
| `SID` | UUID | M | New unique session ID |
| `SID_ORIGINAL` | UUID | M | SID from the original PURCHASE session |
| `PREAUTH_CODE` | `000258` | M | Pre-authorization code returned in PURCHASE STAGE=5 |
| `COMPLETION_AMOUNT` | `12.56` | M | Final charge amount. Must be ≤ original PURCHASE amount. If invalid, terminal returns `STATUS=19` (INVALID AMOUNT) at STAGE=5 |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version, `39`=wrong preauth code, `40`=preauth already completed |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
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
| `APPL_PREF_NAME` | C | Application preferred name |

---

## CANCEL_PREAUTH

Releases the pre-authorization hold on the cardholder's account without charging any amount.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `CANCEL_PREAUTH` | M | |
| `SID` | UUID | M | New unique session ID |
| `SID_ORIGINAL` | UUID | M | SID from the original PURCHASE session |
| `PREAUTH_CODE` | `000258` | M | Pre-authorization code returned in PURCHASE STAGE=5 |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version, `39`=wrong preauth code, `40`=preauth already completed |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
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
| `APPL_PREF_NAME` | C | Application preferred name |

## Pre-Authorization Flow Summary

```
1. PURCHASE with PREAUTH_TX=1
   → Save PREAUTH_CODE and SID_ORIGINAL from STAGE=5 response

2. When final amount known:
   → COMPLETE_PREAUTH (PREAUTH_CODE + COMPLETION_AMOUNT ≤ original)
   OR
   → CANCEL_PREAUTH (release hold, no charge)
```

## Error Codes Specific to Pre-Auth

| STATUS | Meaning |
|--------|---------|
| `39` | WRONG_PREAUTH_CODE — PREAUTH_CODE value is incorrect |
| `40` | PREAUTH_IS_COMPLETED — pre-auth was already completed or cancelled |
| `19` | INVALID AMOUNT — COMPLETION_AMOUNT exceeds the original pre-auth amount |

## See Also

- references/purchase.md — PURCHASE with PREAUTH_TX=1
- references/appendices.md
