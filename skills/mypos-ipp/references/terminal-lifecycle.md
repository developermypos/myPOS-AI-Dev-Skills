# Terminal Lifecycle: ACTIVATE, UPDATE, DEACTIVATE, REPRINT_RECEIPT, GET_CERTIFICATE

## ACTIVATE

Initiates terminal activation — links the terminal to a merchant e-money account. Required when any method returns `STATUS=25` (ACTIVATION REQUIRED). **Always call UPDATE immediately after successful activation** — the terminal will not accept financial requests until updated.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `ACTIVATE` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | `90` (activation can take up to 90s) |
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
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `27`=already activated, `28`=activation unsuccessful, `35`=wrong/expired activation code |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

**After STAGE=5 STATUS=0:** Call UPDATE immediately.

---

## UPDATE

Initiates a terminal software update. Required when:
- Any financial method returns `STATUS=26` (MANDATORY UPDATE REQUIRED) at STAGE=1
- GET_STATUS returns `STATUS=26` or `STATUS=34` at STAGE=5
- Called immediately after ACTIVATE (mandatory post-activation step)

Can also be called at any time from the external interface.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `UPDATE` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `31`=software update do not wait host, `32`=software update wait host |
| `STAGE` | M | `4` |
| `DATA` | C | Binary data to proxy to acquiring host |

**Stage 4 STATUS=31:** Send DATA to host; do **not** wait for a host response. The terminal will send a new STAGE=4 request.
**Stage 4 STATUS=32:** Send DATA to host; **wait** for the host response and send it back in the next STAGE=4.

The STAGE=4 loop is interrupted when STAGE=4 arrives with STATUS≠31 and ≠32, or when STAGE=5 arrives.

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `29`=no updates found, `30`=software update unsuccessful |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## DEACTIVATE

Resets the terminal to factory defaults. The terminal is unlinked from the merchant account. To process payments again, ACTIVATE must be called. If deactivation is incomplete (`STATUS=33` at STAGE=5 or `STATUS=36` at STAGE=1), retry DEACTIVATE.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `DEACTIVATE` | M | |
| `SID` | UUID | M | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `38`=unsupported version |
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
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `33`=deactivation unsuccessful, `35`=wrong/expired code |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## REPRINT_RECEIPT

Reprints the last transaction receipt on the terminal's built-in printer. **Only available on devices with a printer**. Cannot reprint receipts generated with PRINT_EXT.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `REPRINT_RECEIPT` | M | |
| `SID` | UUID | M | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | Note: spec shows `RECEIPT_RECEIPT` as value — this appears to be a typo; echo is `REPRINT_RECEIPT` |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `14`=internal error, `17`=transaction not found, `21`=no printer available, `22`=no paper, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |

---

## GET_CERTIFICATE

**Slave mode only.** Retrieves a DER-encoded SSL certificate for a given fingerprint. When the terminal returns a new fingerprint in any response, call GET_CERTIFICATE to update the local certificate store.

The terminal maintains two certificate chains:
- `PRIMARY_CHAIN` — used for all methods except UPDATE
- `SECONDARY_CHAIN` — used only for the UPDATE method

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `GET_CERTIFICATE` | M | |
| `SID` | UUID | M | |
| `FINGERPRINT` | (20 bytes) | M | Fixed 20-byte fingerprint. No length prefix |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `37`=fingerprint not found, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0` |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `FINGERPRINT` | M | Echoed fingerprint |
| `CERT` | M | DER-encoded certificate. 2-byte big-endian length prefix + certificate bytes (e.g., length 1025 = `0x0401` big-endian) |

## See Also

- references/ping-reboot.md — PING (STATUS=41 also requires UPDATE)
- references/appendices.md — Appendix I (full status codes)
