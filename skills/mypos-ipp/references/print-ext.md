# PRINT_EXT

Prints custom content on the terminal's built-in printer. **Only available on devices with a built-in printer** (e.g., D210). Cannot be used on D200 (no printer). Cannot be used to reprint standard transaction receipts — use REPRINT_RECEIPT for that.

## Request Properties

| Property | Typical Value | Presence | Description |
|----------|--------------|----------|-------------|
| `PROTOCOL` | `IPP` | M | |
| `VERSION` | `202` | M | |
| `METHOD` | `PRINT_EXT` | M | |
| `SID` | UUID | M | |
| `LANG` | `EN` | O | Alpha-2 ISO language code |
| `PRINT_DATA` | (string) | M | Print command string. See PRINT_DATA syntax below |

## Response — Stage 1

| Property | Presence | Description |
|----------|----------|-------------|
| `PROTOCOL` | ME | |
| `METHOD` | ME | |
| `SID` | ME | |
| `STATUS` | M | `0`=OK, `1`=busy, `2`=syntax error, `3`=unsupported param, `4`=unsupported method, `21`=no printer available, `22`=no paper, `23`=incorrect PRINT_DATA, `24`=incorrect logo index, `38`=unsupported version |
| `STAGE` | M | `1` |

## PRINT_DATA Syntax

PRINT_DATA is an ASCII string containing text, escape sequences, and control characters.

### Special Characters

| Character | ASCII | Hex | Role |
|-----------|-------|-----|------|
| ESCAPE (`\f`) | 12 | `0x0C` | Introduces a function code |
| LINE FEED (`\n`) | 10 | `0x0A` | Starts a new line |

### Escape Sequence Format

```
<ESC> <2-byte-ASCII-hex-function-code> <1-byte-ASCII-hex-parameter>
```

Example: `\f` + `01` + `3` = SetFontWeight(double width)

### Functions

#### SetFontWeight — function code `01`

| Parameter | Name | Description |
|-----------|------|-------------|
| `1` | Single Width | Default width (default mode) |
| `2` | Single Height | Default height (default mode) |
| `3` | Double Width | Print next characters at double width |
| `4` | Double Height | Print next characters at double height |

Both SetFontWeight and SetTextAlign persist until changed. Reset to defaults with `\f011` (single width) and `\f012` (single height).

#### SetTextAlign — function code `02`

| Parameter | Name | Description |
|-----------|------|-------------|
| `1` | LEFT | Align text left |
| `2` | CENTER | Center text (default) |
| `3` | RIGHT | Align text right |

Alignment persists until changed.

#### PrintLogo — function code `03`

| Parameter | Value | Description |
|-----------|-------|-------------|
| Logo index | `1`–`F` | Print a pre-loaded logo bitmap at the given index |

### Text Rules

- All non-escape characters are printed as-is.
- Text wider than the supported characters per line wraps to the next line with LEFT alignment.
- Maximum 255 characters before each `\n` (LINE FEED).
- **Printer font supports uppercase letters only** (D210).

### PRINT_DATA Example

```
<ESC>031<LF>THIS IS NORMAL TEXT<LF>
<ESC>013<ESC>014DOUBLE WIDTH+HEIGHT<LF>
<ESC>011<ESC>012<ESC>022CENTER TEXT<LF>
<ESC>023RIGHT ALIGNED<LF>
<ESC>021LEFT ALIGNED<LF>
```

Where `<ESC>` = `0x0C` and `<LF>` = `0x0A`.

Breakdown:
- `\f031` = SetTextAlign(CENTER)
- `\f013` = SetFontWeight(double width), `\f014` = SetFontWeight(double height)
- `\f011` = SetFontWeight(single width), `\f012` = SetFontWeight(single height)
- `\f022` = SetTextAlign(CENTER)
- `\f023` = SetTextAlign(RIGHT)
- `\f021` = SetTextAlign(LEFT)

## Printer Capabilities (Appendix VI)

| Device | Printer | Single-width chars/line | Double-width chars/line | Notes |
|--------|---------|------------------------|------------------------|-------|
| D200 | No | n/a | n/a | PRINT_EXT not supported |
| D210 | Yes | 32 | 16 | Uppercase letters only |

## See Also

- references/terminal-lifecycle.md — REPRINT_RECEIPT (reprint last standard transaction receipt)
- references/appendices.md — Appendix VI (printer capabilities)
