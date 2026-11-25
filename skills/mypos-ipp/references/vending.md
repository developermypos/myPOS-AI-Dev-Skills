# VENDING_PURCHASE, CANCEL_STAGE, and VENDING_READY

---

## VENDING_PURCHASE

Sets the terminal to continuous card-waiting mode, intended for unattended / vending machine deployments. The terminal waits indefinitely for a card to be presented. When a card is processed, `STAGE=5` is returned with the transaction result. After processing, the caller must send `COMPLETE_TX` (with `AMOUNT` and `CURRENCY`) before the terminal re-enters waiting mode or accepts the next transaction.

DCC (Dynamic Currency Conversion) is not presented in vending mode.

Stop the waiting loop by sending `CANCEL_STAGE` (see below).

### Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `VENDING_PURCHASE` | M | |
| `SID` | UUID | M | |
| `AMOUNT` | `2.50` | M | Transaction amount |
| `CURRENCY` | `978` | M | ISO numeric currency code |
| `FIXED_PINPAD` | `1` | O | `1`=fix digit display order on PIN pad (obscures position guessing) |
| `SHOW_AMOUNT` | `1` | O | `0`=display "Pay here" on terminal, `1`=display the amount |
| `LANG` | `EN` | O | |

### Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `20`=not completed last tx, `25`=activation required, `26`=mandatory update required, `36`=deactivation not finished, `38`=unsupported version, `49`=wrong password, `50`=transaction forbidden |
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

### Response — Stage 5 (Transaction Result)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `13`=user cancel, `14`=internal error, `15`=comm error, `18`=reversal not found, `100`=OK with additional info (call GET_STATUS) |
| `STAGE` | M | `5` |
| `TIMEOUT` | O | `0` |
| `TX_STATUS` | C | Terminal transaction status (obsolete). See Appendix III |
| `AUTH_CODE` | C | Authorization code |
| `APPROVAL` | C | `00`=success, other=declined |
| `TX_DATE_LOCAL` | C | Transaction date (YYYY-MM-DD) |
| `TX_TIME_LOCAL` | C | Transaction time (HH:MM:SS) |
| `RRN` | C | Retrieval Reference Number (12 digits) |
| `AMOUNT` | C | Charged amount |
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
| `SIGNATURE_NOT_REQ` | C | Obsolete. Use CVM |
| `CLIENT_TOKEN` | M | 40-byte ASCII hash of merchant+card |
| `DCC_CURRENCY` | C | Not used in vending mode |
| `CVM` | M | Cardholder verification method: `P`=PIN, `S`=signature, `N`=none |
| `ENTRY_MODE` | M | `C`=chip, `S`=swipe, `M`=manual, `P`=contactless |
| `LOGO_TYPE` | M | `1`=none, `2`=myPOS default, `3`=custom |
| `LOGO_GUID` | C | Custom logo GUID (when LOGO_TYPE=3) |
| `PRINTER` | C | `0`=no printer, `1`=available |
| `DECLINED_REASON1` | C | Primary decline reason code. See Appendix IX |
| `DECLINED_REASON2` | C | Secondary decline reason (optional) |

### Notification Stages (Optional)

| Stage | Name | Properties |
|-------|------|-----------|
| `11` | USER_PRESENT_CARD | STATUS=0 — card has been presented |
| `12` | USER_PRESENT_DCC | Not shown in vending mode |
| `13` | USER_PRESENT_PIN | STATUS=0, TRIES_LEFT(C) |
| `14` | USER_SELECTED_DCC | Not shown in vending mode |

### Post-Transaction: COMPLETE_TX Required

After STAGE=5, the caller **must** call COMPLETE_TX with `AMOUNT` and `CURRENCY` before the next financial transaction:

```
COMPLETE_TX request must include:
  SID_ORIGINAL = SID from the VENDING_PURCHASE session
  AMOUNT       = actual charged amount (from VENDING_PURCHASE STAGE=5)
  CURRENCY     = same currency as VENDING_PURCHASE
```

---

## CANCEL_STAGE

Cancels an in-progress operation by sending a stage cancel (`STAGE=101`). Supported for:
- **VENDING_PURCHASE** (always)
- **PURCHASE, REFUND, CASH_ADVANCE** (requires terminal software ≥ 3.45)

### Request Properties

| Property | Value | Presence | Description |
|----------|-------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | (method being cancelled) | M | **Set to the METHOD of the operation to cancel** — e.g., `VENDING_PURCHASE`, `PURCHASE`, `REFUND`, or `CASH_ADVANCE` |
| `SID` | UUID | M | Same SID as the ongoing operation |
| `STAGE` | `101` | M | Signals cancel request |
| `LANG` | `EN` | O | |

### Response

| Property | Presence | Description |
|----------|----------|-------------|
| `METHOD` | ME | Echoes the method that was cancelled |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `38`=unsupported version |
| `STAGE` | M | `5` |

---

## VENDING_READY

The terminal sends VENDING_READY **to the caller** (not the reverse) when the "Initiate payment" setting is enabled on the terminal and the user presses a key to indicate readiness. This is an informational notification — **no response is expected or sent**.

### Message from Terminal (POS)

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | M | `IPP` |
| `VERSION` | M | `202` |
| `METHOD` | M | `VENDING_READY` |
| `SID` | M | UUID generated by the terminal |
| `TIMEOUT` | M | `55` — how long the terminal will wait for the next payment command |

The caller should send a new `VENDING_PURCHASE` within the specified TIMEOUT after receiving VENDING_READY.

## See Also

- references/complete-cancel-tx.md — COMPLETE_TX (required with AMOUNT+CURRENCY after VENDING_PURCHASE)
- references/cash-advance.md — CANCEL_STAGE also applies to CASH_ADVANCE
- references/appendices.md — Appendix III (TX_STATUS), Appendix IX (declined reasons)
