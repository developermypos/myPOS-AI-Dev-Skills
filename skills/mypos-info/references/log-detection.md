# IPP Log Detection and Format Parsing

Use this reference to identify whether a user-provided log is an IPP protocol log, determine its wrapper format, extract the IPP messages, and dispatch to the appropriate analysis tool.

---

## Step 1 — Detect an IPP Log

Scan the content for any of these signals:

| Signal | Confidence |
|--------|-----------|
| Lines start with `DCM:(` | Definite — DCM format IPP log |
| Content contains `MYPOS_RQ:` or `MYPOS_ANSW,LEN=` | Definite — DCM format IPP log |
| Lines labeled `Message:` followed by `PROTOCOL=IPP` | Definite — Partner logger format |
| Content contains `PROTOCOL=IPP` AND `METHOD=` | Definite — IPP log (format unknown) |
| Content contains `VERSION=20` + `SID=` + `STAGE=` | Very likely — IPP log |
| Lines start with `[HH:MM:SS:mmm]<I>` or `[HH:MM:SS:mmm]<D>` | Likely — Partner logger format; check for IPP content |

If none of the above match but the user says "this is an IPP log" or "terminal log" — treat it as an IPP log and describe what you can parse.

---

## Step 2 — Identify the Wrapper Format

### Format A — DCM Logger (most common)

Used by myPOS terminal communication drivers (Windows/.NET, test tools). This is the caller-side log.

**Signature:** Every line starts with `DCM:(HH:MM:SS.mmm)`.

```
DCM:(12:35:22.245)MYPOS_RQ:
DCM:(12:35:22.251)PROTOCOL=IPP
DCM:(12:35:22.258)VERSION=202
DCM:(12:35:22.266)METHOD=PURCHASE
DCM:(12:35:22.273)SID=60DB1389
DCM:(12:35:22.689)MYPOS_ANSW,LEN=92
DCM:(12:35:22.696)PROTOCOL=IPP
DCM:(12:35:22.711)METHOD=PURCHASE
DCM:(12:35:22.717)STAGE=1
DCM:(12:35:22.723)STATUS=0
```

**Parsing:**
1. Strip the `DCM:(HH:MM:SS.mmm)` prefix from every line.
2. Message boundaries: each `MYPOS_RQ:` = start of a new request; each `MYPOS_ANSW,LEN=NNN` = start of a new response.
3. All `KEY=VALUE` lines between two markers belong to the preceding marker's message.
4. `MYPOS_RQ:` lines with no KEY=VALUE content are pure markers.
5. Free-form annotation lines (e.g., `MYPOS CC PRESENTED`, `POS:APPROVED:0.10`) are DCM internal labels — not IPP properties.

Full DCM parsing details and known quirks: `@skills/mypos-ipp/references/debugging.md`

---

### Format B — Partner/Integration Logger

Used by third-party integration applications that log their own IPP communication. Format varies by partner — the example below is from a Qntra-style logger.

**Signature:** Lines start with `[HH:MM:SS:mmm]<LEVEL>` where level is `<I>` (info), `<D>` (debug), `<W>` (warn), `<E>` (error).

```
[16:28:49:380]<I>MyPOS-> Sending to terminal:
[16:28:49:380]<I>RAW: 008C50524F544F434F4C3D495050...
[16:28:49:380]<I>Message: PROTOCOL=IPP\r\nVERSION=200\r\nMETHOD=PURCHASE\r\nSID=AABB1234\r\n
[16:28:49:521]<I>MyPOS-> Input received
[16:28:49:521]<I>RAW: 005A50524F544F434F4C3D495050...
[16:28:49:521]<I>Message: PROTOCOL=IPP\r\nMETHOD=PURCHASE\r\nSTAGE=1\r\nSTATUS=0\r\n
```

**Parsing:**
1. Find lines labeled `Message:` — everything after `Message: ` is the decoded IPP content.
2. Split the decoded content on `\r\n` to get individual `KEY=VALUE` pairs.
3. The `RAW:` line contains the wire-format hex: first 4 hex chars = 2-byte big-endian length, remainder = ASCII bytes of the same KEY=VALUE content. Use `RAW:` only for framing verification, not content parsing.
4. Direction markers like `MyPOS->` indicate request (caller → terminal). Response messages follow the `Input received` pattern.
5. Lines without `Message:` or `RAW:` are application log lines — treat as annotations.

---

### Format C — Raw IPP Text (no wrapper)

Some logs or debug outputs show decoded IPP messages directly without any logging wrapper.

**Signature:** Content contains bare `PROTOCOL=IPP`, `VERSION=`, `METHOD=`, `SID=` lines with no prefix.

```
PROTOCOL=IPP
VERSION=202
METHOD=PURCHASE
SID=60DB1389
AMOUNT=50.00
CURRENCY=978
---
PROTOCOL=IPP
METHOD=PURCHASE
STAGE=1
STATUS=0
TIMEOUT=120
```

**Parsing:** Treat each block of consecutive `KEY=VALUE` lines as one IPP message. Separators (blank lines, dashes, etc.) mark message boundaries.

---

## Step 3 — Dispatch for Analysis

Once you have identified the format and extracted the IPP message blocks (regardless of wrapper), proceed with:

1. Load `@skills/mypos-ipp/references/debugging.md` — apply the Analysis Methodology section (6 steps: confirm format, extract identity, find session boundaries, locate blocks, extract errors, trace APPROVAL).
2. Load `@skills/mypos-ipp/references/appendices.md` — for STATUS code lookups, STAGE code meanings, and AID identification.
3. Apply the APPROVAL Codes table in `@skills/mypos-ipp/references/debugging.md` for any STAGE=5 responses.

**Report structure:**
- Log format identified (DCM / Partner logger / Raw / Unknown)
- Terminal identity extracted (TERMINAL_ID, SERIAL, TERMINAL_NAME if present)
- Session count and boundaries
- All IPP exchanges in order (METHOD → stages → final STATUS/APPROVAL)
- All errors (STATUS≠0) with METHOD, STAGE, and root cause
- All STAGE=5 results with APPROVAL value and analyst action
- Specific bugs or issues found (with fix recommendations)
