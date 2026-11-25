# PING and REBOOT

Both methods require **VERSION=202** and are only available on **Q20/K300** hardware.

---

## PING

Checks the connection between the caller and the terminal. Can also transmit additional data (e.g., for remote activation). If PING returns `STATUS=41` (REMOTELY_ACTIVATED), call `UPDATE` immediately.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `PING` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: SSL certificate fingerprints |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `14`=internal error, `15`=comm error, `16`=SSL error |
| `STAGE` | M | `4` |
| `TIMEOUT` | O | |
| `DATA` | C | Binary data to proxy to acquiring host (when STATUS=0) |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `SID_ORIGINAL` | ME | |
| `STATUS` | M | `0`=OK, `14`=internal error, `15`=comm error, `16`=SSL error, `41`=REMOTELY_ACTIVATED |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

**STATUS=41 (REMOTELY_ACTIVATED):** The terminal was activated remotely since the last session. Call `UPDATE` immediately after receiving this status.

---

## REBOOT

Restarts the terminal device. No user interaction is required.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `REBOOT` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `1` |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

## See Also

- references/terminal-lifecycle.md — UPDATE (required after STATUS=41)
- references/appendices.md — Appendix I (full status codes)
