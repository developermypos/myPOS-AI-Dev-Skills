# GIFTCARD_ACTIVATION and GIFTCARD_DEACTIVATION

Both methods require **VERSION=202** and are available only on **Q20/K300** hardware.

---

## GIFTCARD_ACTIVATION

Registers and loads a new GiftCard with the specified amount. The cardholder presents the gift card at the terminal. On success, the card is active and loaded with the specified amount.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `GIFTCARD_ACTIVATION` | M | |
| `SID` | UUID | M | |
| `AMOUNT` | `50.00` | M | Amount to load onto the GiftCard |
| `CURRENCY` | `978` | M | ISO numeric currency code (e.g., `978`=EUR) |
| `PRINT_RECEIPT` | `0` | O | Receipt behavior. See Appendix VII: `0`=merchant+customer copy, `1`=customer copy on confirmation, `2`=merchant copy only, `3`=no print |
| `LANG` | `EN` | O | |

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
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `APPROVAL` | M | Response code. `00`=success, other=declined |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number (12 digits) |
| `AMOUNT` | C | Amount loaded |
| `CURRENCY` | C | ISO numeric currency code |
| `CURRENCY_NAME` | C | ISO alphabetic currency code |
| `TERMINAL_ID` | C | Terminal ID |
| `MERCHANT_ID` | C | Merchant ID |
| `MERCHANT_NAME` | C | Merchant name |
| `MERCHANT_AL1` | C | Merchant address line 1 |
| `MERCHANT_AL2` | C | Merchant address line 2 |
| `PAN_MASKED` | C | Masked card number |
| `EMBOSS_NAME` | C | Cardholder name |
| `AID` | C | Application Identifier. See Appendix IV |
| `AID_NAME` | C | AID name |
| `APPL_PREF_NAME` | C | Application preferred name |
| `STAN` | C | System Trace Audit Number |
| `PRINTER` | C | `0`=no printer, `1`=available |

---

## GIFTCARD_DEACTIVATION

Unregisters a GiftCard that was sold but not yet activated (e.g., returned item). The card is presented at the terminal and deregistered from the system.

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `GIFTCARD_DEACTIVATION` | M | |
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
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `APPROVAL` | M | Response code. `00`=success, other=declined |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `TX_DATE_LOCAL` | C | Transaction date |
| `TX_TIME_LOCAL` | C | Transaction time |
| `RRN` | C | Retrieval Reference Number |
| `AMOUNT` | C | Amount on the deactivated card |
| `CURRENCY` | C | ISO numeric currency code |
| `CURRENCY_NAME` | C | ISO alphabetic currency code |
| `TERMINAL_ID` | C | Terminal ID |
| `MERCHANT_ID` | C | Merchant ID |
| `MERCHANT_NAME` | C | Merchant name |
| `MERCHANT_AL1` | C | Merchant address line 1 |
| `MERCHANT_AL2` | C | Merchant address line 2 |
| `PAN_MASKED` | C | Masked card number |
| `EMBOSS_NAME` | C | Cardholder name |
| `AID` | C | Application Identifier |
| `AID_NAME` | C | AID name |
| `APPL_PREF_NAME` | C | Application preferred name |
| `STAN` | C | System Trace Audit Number |
| `PRINTER` | C | `0`=no printer, `1`=available |

## See Also

- references/setup.md — protocol framing, Slave mode
- references/appendices.md — Appendix III (TX_STATUS), Appendix IV (AIDs), Appendix VII (receipt config)
