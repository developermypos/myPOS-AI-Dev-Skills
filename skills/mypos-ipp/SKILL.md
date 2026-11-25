---
name: mypos-ipp
description: Guide for implementing the myPOS Integrated Payments Protocol (IPP) — the binary wire protocol for direct serial/USB/Bluetooth/WiFi communication with myPOS terminals. Use when building custom terminal integrations with no available SDK, kiosk/vending scenarios, Cash Register or Slave mode integrations, or when analyzing a DCM protocol log.
license: Apache-2.0
---

# myPOS Integrated Payments Protocol (IPP)

- Spec: `API External Interface Ver. 1.27` (document version 1.27, myPOS AD © 2007–2023)
- Protocol version: 2.7

Use this skill to implement direct communication with myPOS POS terminals over serial, USB, Bluetooth, or WiFi/TCP. IPP is the wire protocol that all myPOS terminal SDKs (Android, iOS, .NET) are built on top of.

## Quick Decision Tree

```
What do you need to do with the terminal?

Payment operations:
├── Accept a card payment
│   ├── Standard (customer pays now) → PURCHASE → references/purchase.md
│   ├── Pre-authorization (hold, complete later) → PURCHASE with PREAUTH_TX=1 → references/purchase.md
│   ├── Vending / unattended terminal → VENDING_PURCHASE → references/vending.md
│   └── Cash advance (Q80 only, EMV only) → CASH_ADVANCE → references/cash-advance.md
│
├── Complete or cancel a transaction
│   ├── Complete after PURCHASE/VENDING_PURCHASE → COMPLETE_TX → references/complete-cancel-tx.md
│   ├── Cancel (auto-reversal of last uncompleted) → CANCEL_TX → references/complete-cancel-tx.md
│   ├── Complete pre-auth with final amount → COMPLETE_PREAUTH → references/preauth.md
│   └── Cancel pre-auth (release hold) → CANCEL_PREAUTH → references/preauth.md
│
├── Refund or reverse
│   ├── Refund (any completed transaction) → REFUND → references/refund.md
│   └── Reversal (last transaction only) → REVERSAL → references/reversal.md
│
└── Payment request / gift cards (VERSION=202, Q20/K300 only)
    ├── Generate payment link → PAYMENT_REQUEST → references/payment-request.md
    ├── Resend reminder → PAYMENT_REQUEST_REMINDER → references/payment-request.md
    ├── Activate GiftCard → GIFTCARD_ACTIVATION → references/giftcard.md
    └── Deactivate GiftCard → GIFTCARD_DEACTIVATION → references/giftcard.md

Status and receipt:
├── Check terminal status or last transaction → GET_STATUS → references/get-status.md
└── Reprint last receipt (built-in printer required) → REPRINT_RECEIPT → references/terminal-lifecycle.md

Custom printing (built-in printer required):
└── Print custom text or logo → PRINT_EXT → references/print-ext.md

Terminal management:
├── Activate terminal (required after STATUS=25) → ACTIVATE → references/terminal-lifecycle.md
├── Update terminal software (required after STATUS=26/34) → UPDATE → references/terminal-lifecycle.md
├── Deactivate terminal (factory reset) → DEACTIVATE → references/terminal-lifecycle.md
├── Get SSL certificate (Slave mode only) → GET_CERTIFICATE → references/terminal-lifecycle.md
├── Connection check (VERSION=202 only) → PING → references/ping-reboot.md
└── Restart terminal (VERSION=202 only) → REBOOT → references/ping-reboot.md

Slave mode internals (terminal-initiated, not caller-initiated):
├── Terminal INIT handshake → INIT / END_INIT → references/init-process.md
├── Terminal busy notification → PROCESS → references/init-process.md
└── Cancel ongoing operation → CANCEL_STAGE (STAGE=101) → references/vending.md

Specialized terminal models only:
├── Display custom text (IM20 only) → DISPLAY_TEXT → references/advanced.md
├── CRR transaction check (Integra+CRR only) → CHECK_TRANSACTION → references/advanced.md
└── Open settings menu → OPEN_SETTINGS → references/advanced.md

Reference tables:
└── Status codes, stage codes, AIDs, languages, decline messages → references/appendices.md

Debugging:
└── Analyze a DCM protocol log → references/debugging.md

Not sure? Start with: references/setup.md
```

## Start Here

1. **Read `references/setup.md`** — covers protocol framing, property ordering rules, connection modes (Cash Register vs Slave), SID generation, and version requirements.
2. **Choose your operation** — use the decision tree to pick the right reference file.
3. **Handle all stage responses** — most operations return STAGE=1 first, then 2/3 for card/PIN interaction, then optionally STAGE=4 (Slave mode), and finally STAGE=5 with the result.
4. **Check APPROVAL=00** — any other value or an empty APPROVAL means the transaction was not successful.
5. **Consult `references/appendices.md`** for status code meanings, supported AIDs, languages, and decline messages.

## Non-Negotiable Rules

- **PROTOCOL must be the first property; METHOD must be the second; DATA must be the last.** Violating ordering causes `STATUS=2` (SYNTAX ERROR).
- **Property names are ALL CAPS and case-sensitive.** `Method` is invalid; `METHOD` is required.
- **Use VERSION=202** for all methods unless a method requires VERSION=200 (DISPLAY_TEXT, CHECK_TRANSACTION, OPEN_SETTINGS).
- **PING, REBOOT, PAYMENT_REQUEST, PAYMENT_REQUEST_REMINDER, SEND_LOG, GIFTCARD_ACTIVATION, GIFTCARD_DEACTIVATION require VERSION=202 and Q20/K300 hardware.**
- **Every PURCHASE / VENDING_PURCHASE / REFUND / CASH_ADVANCE must be followed by COMPLETE_TX** before the next financial transaction. If not, the terminal returns `STATUS=20` (NOT COMPLETED LAST TX). VENDING_PURCHASE additionally requires AMOUNT and CURRENCY in the COMPLETE_TX request.
- **ACTIVATE must always be followed by UPDATE.** After activation, all methods return errors until a successful UPDATE completes.
- **In Slave mode, proxy the DATA property bytes completely unmodified** to the acquiring host — no headers, no framing, no alteration. Only the raw value bytes are forwarded.
- **`STATUS=25` → call ACTIVATE; `STATUS=26` → call UPDATE; `STATUS=36` or `STATUS=33` on DEACTIVATE → retry DEACTIVATE.**
- **`APPROVAL=00` is the only successful transaction result.** An empty `APPROVAL` field means no authorization was attempted or there was a communication breakdown — treat as declined.
- **`STATUS=100` (OK with additional info) → call GET_STATUS** to retrieve the supplementary transaction data.
- **`STATUS=51` (NEED_PASSWORD)** — REFUND, REVERSAL, and PURCHASE with MOTO_TX=1 require a PASSWORD property in the request.

## Implementation Workflow

### Cash Register mode (terminal has own network)
1. Generate a unique SID (UUID) for the session.
2. Build the request: PROTOCOL=IPP, VERSION=202, METHOD=..., SID=..., [params]
3. Frame the message: prepend 2-byte big-endian length, append `\r\n` after each property.
4. Send to terminal; read responses in a loop until STAGE=5.
5. Handle STAGE=1 (validated), STAGE=2 (card waiting), STAGE=3 (PIN), STAGE=5 (result).
6. Check APPROVAL=00 in STAGE=5 response.
7. Call COMPLETE_TX (for financial transactions) with the same SID_ORIGINAL.

### Slave mode (caller provides network)
Same as above, plus:
- Receive IP and PORT from the STAGE=1 response.
- Receive CHAIN for SSL certificate validation.
- When STAGE=4 arrives: open TCP connection to IP:PORT, send the DATA property bytes verbatim, receive response bytes, send them back to the terminal in a new STAGE=4 message with DATA set to the response bytes.
- Repeat STAGE=4 loop until STAGE=5 arrives.

## Required Response Contract

When implementing IPP, always:
- Show the complete request property set for the operation (not just the unique fields)
- Show the response handling for all relevant stages (at minimum: stage 1, stage 5; add 2/3 for payment methods, 4 for Slave mode)
- Check APPROVAL=00 in stage 5 before treating as success
- Include the COMPLETE_TX call after every financial transaction
- Show how STATUS error codes map to recovery actions (ACTIVATE, UPDATE, retry)

## Validation Checklist

- [ ] PROTOCOL is first property, METHOD is second, DATA (if present) is last
- [ ] All property names are ALL CAPS
- [ ] VERSION=202 used (or VERSION=200 for DISPLAY_TEXT/CHECK_TRANSACTION/OPEN_SETTINGS)
- [ ] Unique SID generated per request
- [ ] All stage responses handled (1, 2, 3 where applicable, 4 for Slave, 5)
- [ ] APPROVAL=00 check in STAGE=5 before treating as success
- [ ] COMPLETE_TX called after every PURCHASE/VENDING_PURCHASE/REFUND/CASH_ADVANCE
- [ ] Slave mode: DATA bytes proxied unmodified

## References

- `references/setup.md` — protocol fundamentals (read first)
- `references/purchase.md` — PURCHASE (standard payment + pre-auth + MOTO)
- `references/reversal.md` — REVERSAL
- `references/refund.md` — REFUND
- `references/get-status.md` — GET_STATUS
- `references/complete-cancel-tx.md` — COMPLETE_TX + CANCEL_TX
- `references/preauth.md` — COMPLETE_PREAUTH + CANCEL_PREAUTH
- `references/print-ext.md` — PRINT_EXT (custom receipt printing)
- `references/terminal-lifecycle.md` — ACTIVATE, UPDATE, DEACTIVATE, REPRINT_RECEIPT, GET_CERTIFICATE
- `references/ping-reboot.md` — PING + REBOOT (VERSION=202 only)
- `references/init-process.md` — INIT, END_INIT, PROCESS (Slave mode startup)
- `references/payment-request.md` — PAYMENT_REQUEST, PAYMENT_REQUEST_REMINDER, SEND_LOG (VERSION=202)
- `references/giftcard.md` — GIFTCARD_ACTIVATION + GIFTCARD_DEACTIVATION (VERSION=202)
- `references/vending.md` — VENDING_PURCHASE, CANCEL_STAGE, VENDING_READY
- `references/cash-advance.md` — CASH_ADVANCE (Q80 only)
- `references/advanced.md` — DISPLAY_TEXT (IM20), CHECK_TRANSACTION, OPEN_SETTINGS
- `references/appendices.md` — all status codes, stage codes, AIDs, languages, decline messages
- `references/debugging.md` — reading DCM logs, common failure patterns, annotated example trace
