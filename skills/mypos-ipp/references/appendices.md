# Appendices — Reference Tables

Quick navigation:
- [Appendix I — Status Codes](#appendix-i--status-codes)
- [Appendix II — Stage Codes](#appendix-ii--stage-codes)
- [Appendix III — Transaction Status (Obsolete)](#appendix-iii--transaction-status-obsolete)
- [Appendix IV — Supported AIDs](#appendix-iv--supported-aids)
- [Appendix V — E-Receipt Options](#appendix-v--e-receipt-options)
- [Appendix VI — Printer Capabilities](#appendix-vi--printer-capabilities)
- [Appendix VII — Printed Receipt Configuration](#appendix-vii--printed-receipt-configuration)
- [Appendix VIII — Supported Languages](#appendix-viii--supported-languages)
- [Appendix IX — Transaction Declined Messages](#appendix-ix--transaction-declined-messages)

---

## Appendix I — Status Codes

Returned in the `STATUS` property of all responses.

| Code | Name | Meaning |
|------|------|---------|
| `0` | OK / NO ERROR | Operation successful |
| `1` | TERMINAL BUSY | Terminal is processing another request |
| `2` | SYNTAX ERROR | Malformed request (bad property format, missing required value) |
| `3` | UNSUPPORTED PARAM | Unrecognized property name in request |
| `4` | UNSUPPORTED METHOD | Unrecognized METHOD value |
| `5` | NO CARD FOUND | Card not presented in time |
| `6` | UNSUPPORTED CARD | Card scheme not supported by terminal |
| `7` | CARD CHIP ERROR | Card chip communication error |
| `8` | FALLBACK TO MAGSTRIPE | Internal fallback (not exposed to external caller) |
| `9` | INVALID PIN | PIN entered was incorrect |
| `10` | MAX PIN COUNT EXCEEDED | Cardholder exceeded maximum PIN attempts |
| `11` | PIN CHECK ONLINE | PIN accepted; checking with acquirer |
| `12` | HOST RECEIVE ERROR | Internal host communication error |
| `13` | USER CANCEL | User (cardholder or terminal operator) cancelled |
| `14` | INTERNAL ERROR | Generic terminal internal error |
| `15` | COMMUNICATION ERROR | Network/serial communication failure |
| `16` | SSL ERROR | SSL certificate or TLS handshake error |
| `17` | TRANSACTION NOT FOUND | Referenced transaction (SID_ORIGINAL) not found |
| `18` | REVERSAL NOT FOUND | Reversal target not found |
| `19` | INVALID AMOUNT | Amount is invalid (e.g., COMPLETION_AMOUNT > pre-auth amount) |
| `20` | NOT COMPLETED LAST TX | Previous PURCHASE/REFUND/VENDING_PURCHASE not completed with COMPLETE_TX |
| `21` | NO PRINTER AVAILABLE | Device has no built-in printer |
| `22` | NO PAPER | Printer is out of paper |
| `23` | INCORRECT PRINT_DATA | PRINT_DATA string has invalid format or escape sequences |
| `24` | INCORRECT LOGO INDEX | Logo index in PRINT_DATA references a non-existent logo |
| `25` | ACTIVATION REQUIRED | Terminal must be activated — call ACTIVATE |
| `26` | MANDATORY UPDATE REQUIRED | Terminal must be updated — call UPDATE |
| `27` | TERMINAL IS ALREADY ACTIVATED | ACTIVATE was called but terminal is already active |
| `28` | TERMINAL ACTIVATION IS UNSUCCESSFUL | ACTIVATE failed |
| `29` | NO UPDATES FOUND | UPDATE ran but no new software was available |
| `30` | SOFTWARE UPDATE IS UNSUCCESSFUL | UPDATE failed |
| `31` | SOFTWARE UPDATE DO NOT WAIT HOST | Slave mode: send DATA to host, do **not** wait for host response; terminal will send new STAGE=4 |
| `32` | SOFTWARE UPDATE WAIT HOST | Slave mode: send DATA to host, wait for host response and send it back |
| `33` | DEACTIVATION UNSUCCESSFUL | DEACTIVATE failed; retry |
| `34` | OPTIONAL UPDATE REQUIRED | Terminal can be updated (non-blocking) — update at a convenient time |
| `35` | WRONG CODE | Wrong or expired activation/deactivation code |
| `36` | DEACTIVATION NOT FINISHED | Incomplete deactivation; retry DEACTIVATE before other operations |
| `37` | FINGERPRINT NOT FOUND | GET_CERTIFICATE: fingerprint does not match any certificate in store |
| `38` | UNSUPPORTED PROTOCOL VERSION | VERSION value not supported by this terminal |
| `39` | WRONG PREAUTH CODE | PREAUTH_CODE does not match the stored pre-authorization |
| `40` | PREAUTH IS COMPLETED | Pre-authorization was already completed or cancelled |
| `41` | REMOTELY ACTIVATED | Terminal was activated remotely — call UPDATE immediately |
| `47` | INVALID PAN | MOTO_TX=1 but PAN value is invalid |
| `48` | INVALID EXPIRY DATE | MOTO_TX=1 but EXP_DATE value is invalid |
| `49` | WRONG PASSWORD | Password supplied in PASSWORD property is incorrect |
| `50` | TRANSACTION FORBIDDEN | Operation not allowed in current terminal configuration |
| `51` | NEED PASSWORD | PASSWORD property is required for this operation (REFUND, REVERSAL, or MOTO) |
| `100` | OK / SUCCESS WITH INFO | Transaction completed; call GET_STATUS for additional info |

---

## Appendix II — Stage Codes

Returned in the `STAGE` property of all responses.

| Code | Name | Description |
|------|------|-------------|
| `1` | CHECK REQUEST DATA | Terminal validated the request. No customer interaction yet |
| `2` | CARD HANDLE | Customer card interaction screen is active |
| `3` | PIN HANDLE | Customer PIN entry screen is active |
| `4` | PROCESSING DATA | Slave mode: proxy DATA bytes to acquiring host. May repeat multiple times |
| `5` | PROCESSING OUTCOME | Final result after host communication |
| `11` | USER_PRESENT_CARD | Notification: customer has presented their card |
| `12` | USER_PRESENT_DCC | Notification: DCC (currency choice) screen is displayed |
| `13` | USER_PRESENT_PIN | Notification: customer is entering PIN |
| `14` | USER_SELECTED_DCC | Notification: customer made DCC choice |
| `101` | STAGE_CANCEL | Used in CANCEL_STAGE request to cancel VENDING_PURCHASE (or PURCHASE/REFUND/CASH_ADVANCE with software ≥ 3.45) |

---

## Appendix III — Transaction Status (Obsolete)

The `TX_STATUS` property in STAGE=5 responses is **obsolete** — use only for internal statistics/debugging. Do not use for transaction outcome decisions (use `APPROVAL` instead).

| Code | Meaning |
|------|---------|
| `0` | NONE |
| `1` | HAVE_DATA |
| `2` | SENT2HOST |
| `3` | RECVHOST |
| `4` | NOCARD |
| `5` | RECVHOST_ERROR |
| `6` | USERCANCEL |
| `7` | INTERNAL_ERROR |
| `8` | CONN_HOST_ERROR |
| `9` | SSL_HOST_ERROR |
| `10` | CARD_UNSUPPORTED |
| `11` | CARD_CHIP_ERROR |
| `12` | CARD_EMV_DECLINE |
| `13` | CARD_EMV_APPROVED |
| `14` | CARD_EMV_GO_ONLINE |
| `15` | CARD_EMV_APPROVED_OFFLINE |
| `16` | HAVE_PARTIAL_DATA |
| `17` | REVERSAL_NOT_FOUND |
| `18` | REVERSAL_IS_FOUND |
| `19` | INVALID_REVERSAL_AMOUNT |
| `20` | NOT_SUPPORTED |
| `21` | TX_STATUS_CLEARED |

---

## Appendix IV — Supported AIDs

The `AID` property in STAGE=5 responses contains a hex Application Identifier.

| AID | Card Scheme |
|-----|------------|
| `A0000000043060` | Maestro |
| `A0000000041010` | MasterCard |
| `A0000000031010` | VISA |
| `A0000000032010` | Visa Electron |
| `A0000000032020` | V-PAY |
| `A0000000651010` | JCB |

---

## Appendix V — E-Receipt Options

Used in the `RECEIPT_TYPE` property of `COMPLETE_TX`.

| Value | Behavior |
|-------|---------|
| `1` | No e-receipt — standard printed receipt only |
| `2` | Send e-receipt by email (requires `CREDENTIAL` with email address) |
| `3` | Send e-receipt by SMS (requires `CREDENTIAL` with mobile number) |

When `RECEIPT_TYPE=2` or `3`, `CREDENTIAL` must be set. When `RECEIPT_TYPE=1`, `CREDENTIAL` must be empty.

---

## Appendix VI — Printer Capabilities

| Device Model | Has Printer | Single Width Chars/Line | Double Width Chars/Line | Notes |
|-------------|-------------|------------------------|------------------------|-------|
| D200 | No | n/a | n/a | PRINT_EXT not supported on this model |
| D210 | Yes | 32 | 16 | Uppercase ASCII characters only |

---

## Appendix VII — Printed Receipt Configuration

Used in the `PRINT_RECEIPT` property of PURCHASE, REFUND, GIFTCARD_ACTIVATION, CASH_ADVANCE.

| Value | Behavior |
|-------|---------|
| `0` | Print merchant's copy AND customer's copy automatically (default) |
| `1` | Print customer's copy only after confirmation by operator |
| `2` | Print merchant's copy only |
| `3` | Do not print any receipts |

---

## Appendix VIII — Supported Languages

Used in the `LANG` property. Two-letter ISO 639-1 codes.

| Code | Language | Character Encoding |
|------|----------|-------------------|
| `EN` | English | Windows-1251 |
| `BG` | Bulgarian | Windows-1251 |
| `IT` | Italian | Windows-1251 |
| `HR` | Croatian | Windows-1250 |
| `RO` | Romanian | Windows-1250 |
| `SI` | Slovenian | Windows-1250 |
| `CZ` | Czech | Windows-1250 |
| `PL` | Polish | Windows-1250 |
| `HU` | Hungarian | Windows-1250 |
| `ES` | Spanish | Windows-1252 |
| `DE` | German | Windows-1252 |
| `FR` | French | Windows-1252 |
| `NL` | Dutch | Windows-1252 |
| `SE` | Swedish | Windows-1252 |
| `PT` | Portuguese | Windows-1252 |
| `IS` | Icelandic | Windows-1252 |
| `DK` | Danish | Windows-1252 |
| `NO` | Norwegian | Windows-1252 |
| `GR` | Greek | Windows-1253 |
| `LV` | Latvian | Windows-1257 |
| `LT` | Lithuanian | Windows-1257 |

---

## Appendix IX — Transaction Declined Messages

Returned in `DECLINED_REASON1` and optionally `DECLINED_REASON2` in STAGE=5 responses.

| Code | Reason | Recommended Action |
|------|--------|-------------------|
| `04` | Capture card | Retain card if possible; contact myPOS support |
| `41` | Lost card | Retain card; contact myPOS support |
| `43` | Stolen card | Retain card; contact myPOS support |
| `51` | Not sufficient funds | Cardholder must contact their bank |
| `54` | Expired card | Check embossed date: if future, retry; if past, return card to customer |
| `55` | Incorrect PIN | Cardholder re-enters PIN |
| `57` | Transaction not permitted by issuer | Cardholder contacts their bank |
| `58` | Transaction not permitted on terminal | Check forbidden operation settings or contact myPOS support |
| `61` | Exceeds withdrawal limit | Cardholder contacts bank |
| `62` | Restricted card | Cardholder contacts bank |
| `63` | Security violation | Cardholder contacts bank |
| `65` | Exceeds withdrawal frequency | Cardholder contacts bank |
| `75` | PIN tries exceeded | Cardholder contacts bank; request alternate payment method |
| `AF` | Transaction declined / authentication error | Generic decline; offer alternative payment |
| `CC` | Customer cancellation | Customer cancelled intentionally |
| `EE` | Encryption error | Technical error; retry or contact support |
| `TR` | Transaction declined | Generic decline |
| `TR-02` | Card removed during EMV | Customer removed card during chip processing; retry |
| `TR-11` | Card declined | Card issuer declined |
