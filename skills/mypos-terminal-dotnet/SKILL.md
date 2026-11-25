---
name: mypos-terminal-dotnet
description: Use when a developer needs to control a myPOS POS terminal from a .NET application over USB/Serial or TCP-IP
license: Apache-2.0
---

# myPOS .NET SDK (Terminal Control)

> Docs: https://developers.mypos.com/sdks/dotnet-sdk
> Integration guide: https://developers.mypos.com/in-person-payments/paying-counter

## Quick Decision Tree

```
.NET app controlling a separate myPOS terminal?
│
├── Connection type?
│   ├── USB / Serial port → Initialize("COM3") → references/connection.md
│   └── TCP-IP / WiFi → Initialize(tcpStream) → references/connection.md
│
├── Accept a card payment → Purchase() → references/payment.md
│
├── Refund or void?
│   ├── Refund → Refund() (password required)
│   └── Same-day cancel → Reversal() → references/refund-reversal.md
│
├── Hold funds first?
│   └── Preauthorization() / CompletePreauth() / CancelPreauth()
│       → references/preauthorization.md
│
├── Gift cards → GiftcardActivation / Deactivation / CheckBalance
│   → references/gift-cards.md
│
├── Display or printing?
│   └── DisplayTextOnScreen / PrintExternal / ReprintReceipt
│       → references/receipts-display.md
│
├── Terminal lifecycle?
│   └── Activate / Deactivate / Update / GetStatus / Ping
│       → references/terminal-management.md
│
└── Advanced?
    └── MoTo / Twint / Vending / OriginalCredit / SendPaymentRequest
        → references/advanced.md
```

## Start Here

1. Add `myPOSTerminal.dll` (v2.0.0.1) as a project reference — it is not on NuGet.
2. Add `using myPOS;` to your file.
3. Instantiate `myPOSTerminal terminal = new myPOSTerminal();`
4. **Subscribe to `ProcessingFinished` event BEFORE calling any transaction method.** Without it you will never receive the result.
5. Call `Initialize("COM3")` for USB, or `Initialize(tcpStream)` for TCP-IP.
6. Read `references/connection.md`, then `references/payment.md`.

## Non-Negotiable Rules

- **Subscribe to `ProcessingFinished` before any transaction.** Transaction methods return a `RequestResult` enum immediately (not the transaction outcome) — the actual approved/declined result arrives in the event callback.
- **Card data is never exposed.** Only masked PANs (`e.MaskedPAN`) and approval codes are returned. Never attempt to read raw card data.
- **`SetPassword()` is required** before calling `Refund()`, `Reversal()`, or `CashAdvance()`. Without it those calls fail.
- **Handle the `RequestResult` enum.** A result of `RequestResult.Processing` means the request was accepted; any other value (Busy, InvalidParams, NotInitialized) means the operation did not start — handle it before waiting for the event.
- **One transaction at a time.** The terminal is busy while processing — check `RequestResult.Busy` and queue or block concurrent calls.
- **Demo Debug POS device required for testing** — request one from the myPOS sales team.

## Implementation Workflow

1. Reference DLL, add `using myPOS;`
2. Instantiate terminal, subscribe to `ProcessingFinished` (and optionally `onPresentCard`, `onPresentPin`, etc.)
3. Connect with `Initialize()`
4. Call transaction method — handle `RequestResult` immediately
5. In `ProcessingFinished` callback — check `e.Approved`, use `e.TransactionReference` and `e.MaskedPAN`
6. Call `Disconnect()` when done

## Required Response Contract

When helping implement this SDK, always:
- Show the `ProcessingFinished` event subscription and handler
- Show the `RequestResult` check immediately after each transaction call
- Show `terminal.Initialize()` with the correct overload for the connection type
- Note that `SetPassword()` is required for Refund/Reversal/CashAdvance
- Include `terminal.Disconnect()` in cleanup / using pattern

## Validation Checklist

- [ ] `ProcessingFinished` event subscribed before first transaction call
- [ ] `RequestResult` checked after every transaction method call
- [ ] `SetPassword()` called before Refund, Reversal, CashAdvance
- [ ] Concurrent transaction calls guarded (check for `Busy`)
- [ ] `Disconnect()` called on application exit / terminal release
- [ ] Demo Debug POS device used for testing (not a production terminal)

## References

See `references/README.md` for the full index and reading order.
