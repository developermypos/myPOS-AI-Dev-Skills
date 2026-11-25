# PAYMENT_REQUEST, PAYMENT_REQUEST_REMINDER, and SEND_LOG

All three methods require **VERSION=202** and are available only on **Q20/K300** hardware.

---

## PAYMENT_REQUEST

Generates a payment request link and sends it to a recipient via SMS or email. The recipient can use the link to pay the amount remotely.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `PAYMENT_REQUEST` | M | |
| `SID` | UUID | M | |
| `AMOUNT` | `25.00` | M | Payment amount |
| `CURRENCY` | `978` | M | ISO numeric currency code (e.g., `978`=EUR) |
| `RECIPIENT_GSM` | `+359888123456` | O | Recipient mobile number in international format. At least one of RECIPIENT_GSM or RECIPIENT_EMAIL must be provided |
| `RECIPIENT_EMAIL` | `user@example.com` | O | Recipient email address. At least one of RECIPIENT_GSM or RECIPIENT_EMAIL must be provided |
| `REQUEST_REASON` | `Invoice #123` | O | Description or reason for the payment request |
| `EXP_DAYS` | `30` | O | Expiry in days. Valid range: 1–180. Values outside range default to 30. Default: 30 |
| `LANG` | `EN` | O | Language for terminal display |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
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
| `STATUS` | M | `0`=OK |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## PAYMENT_REQUEST_REMINDER

Resends a reminder for an existing payment request that was previously generated with PAYMENT_REQUEST. The original `REQUEST_CODE` is required.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `PAYMENT_REQUEST_REMINDER` | M | |
| `SID` | UUID | M | |
| `REQUEST_CODE` | `PR12345` | M | Code identifying the previously generated payment request |
| `RECIPIENT_GSM` | `+359888123456` | O | Resend to this mobile number (may differ from original) |
| `RECIPIENT_EMAIL` | `user@example.com` | O | Resend to this email (may differ from original) |
| `LANG` | `EN` | O | |

### Response — Stage 1

Same structure as PAYMENT_REQUEST Stage 1.

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode |
| `PORT` | C | Slave mode |
| `CHAIN` | C | Slave mode |

### Response — Stage 4 (Slave Mode)

Same as PAYMENT_REQUEST Stage 4.

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

---

## SEND_LOG

Transmits terminal diagnostic logs to myPOS servers. Used for remote diagnostics and support purposes.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `SEND_LOG` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `1` |
| `TIMEOUT` | O | |
| `IP` | C | Slave mode: acquiring host address |
| `PORT` | C | Slave mode: port |
| `CHAIN` | C | Slave mode: **fixed 20 bytes** (no `;`-separated chain for SEND_LOG) |

### Response — Stage 4 (Slave Mode)

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `4` |
| `DATA` | C | Binary data to proxy to host |

**Note:** For SEND_LOG, the `CHAIN` property in Stage 1 contains a single fixed 20-byte fingerprint (no `;` separator), unlike other methods that can return up to 4 fingerprints.

### Response — Stage 5

| Property | Presence | Description |
|----------|----------|-------------|
| `STATUS` | M | `0`=OK |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |

## See Also

- references/setup.md — Slave mode DATA proxy rules
- references/appendices.md — Appendix I (status codes)
