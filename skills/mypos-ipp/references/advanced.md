# DISPLAY_TEXT, CHECK_TRANSACTION, and OPEN_SETTINGS

These methods use **VERSION=200** (not 202). They are specialized for specific terminal configurations.

---

## DISPLAY_TEXT

Shows custom text on the terminal screen. **Only available on myPOS Integra (IM20).** Supports up to 5 rows of UTF-8 text, each with independent alignment.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `200` | M | **Must be 200, not 202** |
| `METHOD` | `DISPLAY_TEXT` | M | |
| `SID` | UUID | M | |
| `DISPLAY_TEXT_ROW1` | `Hello!` | C | First text row. UTF-8, max 40 characters. At least one row is required |
| `DISPLAY_TEXT_ROW2` | `Welcome.` | C | Second text row |
| `DISPLAY_TEXT_ROW3` | | C | Third text row |
| `DISPLAY_TEXT_ROW4` | | C | Fourth text row |
| `DISPLAY_TEXT_ROW5` | | C | Fifth text row |
| `DISPLAY_TEXT_ROW1_ALIGN` | `2` | C | Alignment for row 1: `1`=left, `2`=center (default), `3`=right |
| `DISPLAY_TEXT_ROW2_ALIGN` | `2` | C | Alignment for row 2 |
| `DISPLAY_TEXT_ROW3_ALIGN` | `2` | C | Alignment for row 3 |
| `DISPLAY_TEXT_ROW4_ALIGN` | `2` | C | Alignment for row 4 |
| `DISPLAY_TEXT_ROW5_ALIGN` | `2` | C | Alignment for row 5 |
| `DISPLAY_TEXT_TIMEOUT` | `10` | O | Time to display text in seconds. Range: 1–15. Default: 10 |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update, `36`=deactivation not finished, `38`=unsupported version, `49`=wrong password, `50`=transaction forbidden |
| `STAGE` | M | `1` |
| `TIMEOUT` | M | `55` |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update, `36`=deactivation not finished, `38`=unsupported version, `49`=wrong password, `50`=transaction forbidden |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## CHECK_TRANSACTION

Manages communication between the myPOS Integra terminal and its master and CRR (Cash Register Remote) units. **Only for myPOS Integra + CRR (master) configuration.** Uses VERSION=200.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `200` | M | **Must be 200** |
| `METHOD` | `CHECK_TRANSACTION` | M | |
| `SID` | UUID | M | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | M | `85` |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## OPEN_SETTINGS

Opens the settings menu on the terminal screen, allowing the user to configure terminal options. Uses VERSION=200.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `200` | M | **Must be 200** |
| `METHOD` | `OPEN_SETTINGS` | M | |
| `SID` | UUID | M | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `17`=transaction not found, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | M | `55` |

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `17`=transaction not found, `25`=activation required, `36`=deactivation not finished, `38`=unsupported version |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

## Version Note

These three methods use `VERSION=200`, unlike most other methods which use `VERSION=202`. Sending `VERSION=202` to these methods will result in `STATUS=38` (UNSUPPORTED PROTOCOL VERSION).

## See Also

- references/setup.md — Protocol versioning overview
- references/appendices.md — Appendix I (full status codes)
