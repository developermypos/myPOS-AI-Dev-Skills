# Debugging IPP Protocol Logs (DCM Format)

The DCM logger is the standard log format used by myPOS terminal communication drivers. When a user provides a DCM log, use this reference to reconstruct the message exchange, identify the stage flow, and diagnose failures.

---

## Analysis Methodology

**Before analyzing:**
- Process the **entire** log — never sample or skip to the end.
- Report only what the log contains — do not infer missing fields or assume defaults.
- When you encounter an unknown code or value, report it as-is. Do not substitute a guess.
- If timestamps appear out of order, note it without silently reordering.

**Reading a DCM log — six steps:**

1. **Confirm format.** Every line should start with `DCM:(HH:MM:SS.mmm)`. If some lines look different (different prefix, no timestamp), note them before continuing — they may be from a different logger or a concatenated file.
2. **Extract terminal identity.** Scan all IPP blocks for `SERIAL`, `TID` (TERMINAL_ID), `MID` fields. Note `TERMINAL_NAME` if present — it indicates hardware model and connection type.
3. **Find session boundaries.** In Slave mode each session starts with a `MYPOS_RQ:` block where `METHOD=INIT`. In Cash Register mode, the first `MYPOS_RQ:` begins the session.
4. **Locate IPP message blocks.** Each message starts at a `MYPOS_RQ:` (request) or `MYPOS_ANSW,LEN=NNN` (response) line. Strip the `DCM:(timestamp)` prefix; all `KEY=VALUE` lines up to the next marker belong to that message. Blank lines between messages may or may not be present.
5. **Extract errors.** For every response block, check `STATUS`. Any STATUS≠0 is an error — record the METHOD, STAGE, and STATUS value together. Do not stop at the first error; scan the full log.
6. **Trace APPROVAL codes.** For every `STAGE=5` response, record the `APPROVAL` value. `APPROVAL=00` is the only successful result. Any other value or an absent APPROVAL field means the transaction was not completed. Cross-reference the APPROVAL Codes table below.

---

## Log Format

Each line follows this pattern:

```
DCM:(HH:MM:SS.mmm)CONTENT
```

Three types of content:

| Type | Example | Meaning |
|------|---------|---------|
| Request marker | `DCM:(12:35:22.245)MYPOS_RQ:` | Start of a caller→terminal message |
| Response marker | `DCM:(12:35:22.689)MYPOS_ANSW,LEN=92` | Start of a terminal→caller message. LEN = total message size in bytes |
| Property line | `DCM:(12:35:22.258)VERSION=202` | A single `KEY=VALUE` property belonging to the current message |
| Free-form annotation | `DCM:(12:35:32.160)MYPOS CC PRESENTED` | Internal DCM label — not an IPP message property |

**Finding message boundaries:** A new message begins at each `MYPOS_RQ:` or `MYPOS_ANSW,LEN=NNN` line. Blank lines between messages may or may not be present — do not rely on blank lines as delimiters.

**Parsing a message:** Strip the `DCM:(timestamp)` prefix from each line. All `KEY=VALUE` lines that follow a message marker (up to the next `MYPOS_RQ:` or `MYPOS_ANSW` marker) belong to that message.

**Timestamps:** Each property line carries its own timestamp. Lines within one message span only a few milliseconds — these per-property timestamps are not individually significant. Use the message marker timestamp for timing comparisons.

**LEN field:** The number after `MYPOS_ANSW,LEN=` is the total byte length of the decoded IPP response frame (2-byte length header + all `KEY=VALUE\r\n` pairs). Useful for sanity-checking framing but not required to parse the message content.

---

## Known Free-Form Annotation Lines

These lines appear in DCM logs but are **not IPP properties** — they are internal DCM status labels. Ignore them when reconstructing IPP messages.

| Annotation | Meaning |
|-----------|---------|
| `MYPOS_RQ:` | Request message start marker |
| `MYPOS_ANSW,LEN=NNN` | Response message start marker |
| `MYPOS CC PRESENTED` | Contactless card was tapped at the terminal |
| `MYPOS SETTLEMENT START` | Caller is about to send COMPLETE_TX |
| `MYPOS COMM TIMEOUT!` | Caller-side communication timeout (no response received in time) |
| `RRN=...,ACODE=...` | DCM internal summary of the approved transaction |
| `POS:APPROVED:AMOUNT` | DCM internal approval confirmation |
| `POS:STAN=NNN` | STAN value from DCM internal state |
| `POS:CARD=SCHEME` | Card scheme from DCM internal state |
| `POS:CARD#=**NNNN` | Masked PAN from DCM internal state |
| `POS:CREDIT TO VM:AMOUNT` | Internal ledger entry (VM = virtual merchant) |

---

## Real-World Fields Not in Spec

Real terminal logs often include fields that are not documented in the IPP spec. Treat them as extra metadata — do not error on unknown fields.

| Field | Example | Notes |
|-------|---------|-------|
| `TERMINAL_NAME` | `DEMO D200 RS232` | Human-readable model + connection type. Useful for identifying hardware |
| `CONFIG_LOGO` | `MYPOS` | Logo configuration label |

---

## Known Quirks

- **STAGE=5 may appear twice in sequence** with identical content and close timestamps (~300ms apart). This is a DCM logger artifact (message logged once on receive, once on processing). It is **not** a terminal retransmission. Process only the first occurrence.
- **STAGE=11 (USER_PRESENT_CARD) precedes STAGE=2** in the normal flow — the notification that a card has been presented arrives before the card-waiting stage message. This is correct per spec.
- **STAGE=3 can appear even when CVM=N** — the terminal may send STAGE=3 for an online PIN check that the host waives for low-value contactless transactions.

---

## Annotated Example Trace

Source: `ipp_logs.txt`

```
12:35:22.245  MYPOS_RQ:                       ← PURCHASE request starts
12:35:22.251  PROTOCOL=IPP
12:35:22.258  VERSION=202
12:35:22.266  METHOD=PURCHASE
12:35:22.273  SID=60DB1389
12:35:22.280  AMOUNT=0.10
12:35:22.287  CURRENCY=946                    ← 946 = RON (Romanian Leu)
```
**Analysis:** Cash Register mode PURCHASE for RON 0.10.

---

```
12:35:22.689  MYPOS_ANSW,LEN=92              ← Response, 444ms round-trip
12:35:22.696  PROTOCOL=IPP
12:35:22.711  METHOD=PURCHASE
12:35:22.717  STAGE=1
12:35:22.723  STATUS=0                        ← OK — terminal accepted
12:35:22.730  SID=60DB1389
12:35:22.737  TIMEOUT=120
```
**Analysis:** STAGE=1 STATUS=0. No IP/PORT/CHAIN → **Cash Register mode** (terminal manages its own network). TIMEOUT=120 tells caller to expect up to 120 seconds for the next response.

---

```
12:35:23.377  MYPOS_ANSW,LEN=92
12:35:23.399  METHOD=PURCHASE
12:35:23.406  STAGE=11
12:35:23.412  STATUS=0                        ← STAGE=11 = USER_PRESENT_CARD notification
```
**Analysis:** Customer tapped their card at the terminal (notification stage). This is informational only — no action required from caller.

---

```
12:35:32.104  MYPOS_ANSW,LEN=91              ← 8.7s after stage 11 — customer was slow to tap
12:35:32.133  STAGE=2
12:35:32.139  STATUS=0
12:35:32.152  TIMEOUT=85
12:35:32.160  MYPOS CC PRESENTED             ← free-form annotation: contactless
```
**Analysis:** STAGE=2 = card interaction underway. The 8.7s gap between STAGE=11 and STAGE=2 is the time between the notification and the card being fully read.

---

```
12:35:32.802  MYPOS_ANSW,LEN=91
12:35:32.831  STAGE=3
12:35:32.837  STATUS=0
12:35:32.850  TIMEOUT=45
```
**Analysis:** STAGE=3 = PIN/online check. STATUS=0 = proceeding with online check. (Final CVM=N indicates the host waived PIN for this low-value contactless transaction.)

---

```
12:35:49.022  MYPOS_ANSW,LEN=709            ← 16.2s — host processing time
12:35:49.051  STAGE=5
12:35:49.057  STATUS=0
12:35:49.094  APPROVAL=00                    ← APPROVED
12:35:49.130  AMOUNT=0.10
12:35:49.146  CURRENCY_NAME=RON
12:35:49.155  TERMINAL_ID=90007482
12:35:49.167  TERMINAL_NAME=DEMO D200 RS232  ← real-world field; not in spec
12:35:49.297  CVM=N                          ← no CVM required
12:35:49.304  ENTRY_MODE=P                   ← P = contactless
12:35:49.326  PRINTER=0                      ← D200 has no built-in printer
12:35:49.341  CONFIG_LOGO=MYPOS              ← real-world field; not in spec
12:35:49.334  LOGO_TYPE=2                    ← 2 = myPOS default logo
```
**Analysis:** STAGE=5 STATUS=0, APPROVAL=00 → **transaction approved**. ENTRY_MODE=P confirms contactless. PRINTER=0 confirms D200 model (no printer).

---

```
12:35:49.349  MYPOS_ANSW,LEN=709            ← DUPLICATE of previous STAGE=5, 327ms later
12:35:49.378  STAGE=5
12:35:49.384  STATUS=0
... (identical content) ...
```
**Analysis:** This is a **DCM logging artifact** — the same STAGE=5 message is logged twice. Ignore this second occurrence; it does not indicate a terminal retransmission or an error.

---

```
12:35:49.669  RRN=118012422858,ACODE=064890  ← free-form; DCM internal summary
12:35:49.679  POS:APPROVED:0.10              ← free-form; DCM internal
...
12:35:49.719  MYPOS SETTLEMENT START         ← free-form; about to send COMPLETE_TX
```

---

```
12:35:49.726  MYPOS_RQ:                      ← COMPLETE_TX request starts
12:35:49.740  VERSION=202
12:35:49.748  METHOD=COMPLETE_TX
12:35:49.755  SID=60DB1389                   ← BUG #1: reusing the PURCHASE SID
12:35:49.764  SID_ORIGINAL=2000261A          ← BUG #2: wrong value (should be 60DB1389)
```
**Analysis: Two bugs.**
- `SID=60DB1389` reuses the PURCHASE session ID. COMPLETE_TX requires a **new unique SID**.
- `SID_ORIGINAL=2000261A` should be `60DB1389` — the SID from the PURCHASE session that this COMPLETE_TX is completing. The values appear to be swapped or miscalculated in the caller's code.

---

```
12:35:50.800  MYPOS_ANSW,LEN=117
12:35:50.829  STAGE=1
12:35:50.835  STATUS=3                        ← UNSUPPORTED PARAM — request rejected
12:35:50.858  TIMEOUT=25
```
**Analysis:** STATUS=3 = UNSUPPORTED PARAM. The incorrect SID/SID_ORIGINAL combination caused the terminal to reject the COMPLETE_TX.

---

```
12:36:50.952  MYPOS COMM TIMEOUT!            ← 60s after the error — caller never handled STATUS=3
```
**Analysis: Missing error handling.** The caller received STATUS=3 in STAGE=1 but did not inspect it. It continued waiting for subsequent stage messages that would never arrive, eventually hitting a 60-second timeout. **Every STAGE=1 response must be checked for STATUS≠0 and handled immediately before waiting for further stages.**

---

## Common Failure Patterns

| Symptom in log | Root cause | Fix |
|----------------|-----------|-----|
| COMPLETE_TX STAGE=1 STATUS=3 | SID and SID_ORIGINAL are swapped or wrong | SID must be a **new** unique ID; SID_ORIGINAL must be the exact SID used in the PURCHASE/REFUND request |
| COMPLETE_TX STAGE=1 STATUS=17 | SID_ORIGINAL not found on terminal | Use the SID that was sent in the PURCHASE request, not any other value |
| Any method STAGE=1 STATUS=20 | COMPLETE_TX was not called after previous PURCHASE/REFUND/VENDING_PURCHASE | Call COMPLETE_TX for the pending transaction before sending a new one |
| Any method STAGE=1 STATUS=25 | Terminal not activated | Call ACTIVATE → immediately followed by UPDATE |
| Any method STAGE=1 STATUS=26 | Mandatory software update required | Call UPDATE |
| Any method STAGE=1 STATUS=36 | Previous DEACTIVATE did not complete | Retry DEACTIVATE |
| Any method STAGE=1 STATUS=1 | Terminal busy with another operation | Wait and retry |
| Any method STAGE=1 STATUS=38 | VERSION in request not supported by this terminal | Check terminal firmware; use VERSION=202 for current terminals |
| PURCHASE STAGE=5 APPROVAL≠00 or empty APPROVAL | Transaction declined or not attempted | Check DECLINED_REASON1/DECLINED_REASON2 (Appendix IX). Empty APPROVAL = no authorization attempt |
| PURCHASE STAGE=5 STATUS=100 | Transaction completed with additional info | Call GET_STATUS immediately to retrieve supplementary transaction data |
| COMM TIMEOUT after error STATUS | Caller not checking STATUS in STAGE=1 | After receiving any stage response, check STATUS before waiting for the next stage. Non-zero STATUS = stop and handle the error |
| Duplicate STAGE=5 in log | DCM logging artifact | Normal — ignore the second occurrence; it is not a retransmission |

## APPROVAL Codes

`APPROVAL=00` is the only successful result. All other values mean the transaction was not authorized. Appear in `STAGE=5` responses.

| Code | Meaning | Analyst Action |
|------|---------|---------------|
| `00` | Approved | Transaction successful |
| `01` | Refer to card issuer | Cardholder must call their bank |
| `02` | Refer to card issuer, special condition | Cardholder must call their bank |
| `03` | Invalid merchant | Check terminal configuration |
| `04` | Capture card | Retain card; contact acquirer support |
| `05` | Do not honor | Issuer declined; cardholder contacts bank |
| `06` | Error | Generic error; retry |
| `07` | Pick up card, special condition | Retain card |
| `08` | Honor with identification | Verify cardholder ID |
| `09` | Request in progress | Retry after delay |
| `10` | Partial approval | Partial amount approved — caller must handle split tender |
| `11` | VIP approval | Approved |
| `12` | Invalid transaction | Check transaction type / METHOD parameters |
| `13` | Invalid amount | Check AMOUNT field |
| `14` | Invalid card number | PAN invalid or Luhn check failed |
| `15` | No such issuer | BIN not recognized |
| `25` | Unable to locate record | Transaction not found (expected in REVERSAL when no reversible tx exists) |
| `28` | File temporarily unavailable | Retry later |
| `30` | Format error | Message format error |
| `33` | Expired card | Card past expiry |
| `34` | Suspected fraud | Issuer flagged transaction; cardholder contacts bank |
| `35` | Card acceptor, contact acquirer | Contact acquirer support |
| `36` | Restricted card | Card restricted by issuer |
| `37` | Card acceptor, call acquirer security | Security issue; call acquirer |
| `38` | Allowable PIN tries exceeded | Cardholder exceeded PIN attempts |
| `39` | No credit account | Card has no credit facility |
| `40` | Requested function not supported | Operation not supported on this card |
| `41` | Lost card | Retain card |
| `43` | Stolen card | Retain card |
| `51` | Insufficient funds | Cardholder contacts bank |
| `54` | Expired card | Check embossed date |
| `55` | Invalid PIN | Wrong PIN entered |
| `57` | Transaction not permitted to cardholder | Issuer restriction; cardholder contacts bank |
| `58` | Transaction not permitted to terminal | Terminal not configured for this operation |
| `61` | Exceeds withdrawal amount limit | Cardholder contacts bank |
| `62` | Restricted card | Card use restricted |
| `63` | Security violation | Security check failed |
| `65` | Exceeds withdrawal frequency limit | Too many withdrawals |
| `75` | Allowable number of PIN tries exceeded | Cardholder contacts bank |
| `76` | Invalid/non-existent account | Account not found |
| `77` | Invalid/non-existent account | Same as 76 |
| `78` | Invalid/non-existent account | Blocked card |
| `85` | No reason to decline | Approved (verification-only; no funds moved) |
| `91` | Issuer or switch inoperative | Issuer unavailable; retry later |
| `92` | Routing failure | Intermediate network cannot find issuer |
| `93` | Transaction cannot be completed | Violation of law |
| `94` | Duplicate transmission | Duplicate request detected (expected in REVERSAL for already-reversed tx) |
| `96` | System malfunction | Issuer system error; retry |
| `AF` | Authentication failed | 3DS/EMV authentication failure |
| `TR` | Transaction declined | Generic decline |
| `TR-02` | Card removed during EMV | Customer removed card mid-transaction |
| `TR-11` | Card declined | Issuer declined via EMV |
| `CC` | Customer cancellation | Customer pressed cancel on terminal |
| `EE` | Encryption error | Terminal cryptography error |

---

## See Also

- references/appendices.md — full STATUS code table (Appendix I), STAGE codes (Appendix II), decline messages (Appendix IX)
- references/complete-cancel-tx.md — COMPLETE_TX correct usage (SID_ORIGINAL rules)
- references/get-status.md — how to recover after STATUS=100
