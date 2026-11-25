---
name: mypos-terminal-android
description: Guide for building Android apps that control myPOS payment terminals remotely over Bluetooth, USB, or TCP/IP using the myPOS Android Slave SDK. Use when implementing a cash register, kiosk, or point-of-sale app on a separate Android device that drives a connected myPOS terminal for payments, refunds, pre-authorization, gift cards, receipts, terminal management, or card tokenization.
license: Apache-2.0
---

# myPOS Android SDK (Slave / Cash Register)

- Docs root: https://developers.mypos.com/
- SDK overview: https://developers.mypos.com/sdks/android-sdk
- Integration context: https://developers.mypos.com/in-person-payments/paying-counter

Use this skill to build an Android "master" app that controls a myPOS terminal ("slave") over Bluetooth, USB, or TCP/IP. The terminal handles all card operations — your app initiates them.

## Quick Decision Tree

```
What do you need to do?
├─ Connect to the terminal → references/connection.md
├─ Take a card payment → references/payment.md
├─ Refund or reverse a payment → references/refund-reversal.md
├─ Hold funds; complete or cancel later → references/preauthorization.md
├─ Send a payment request by email or SMS → references/payment-requests.md
├─ Gift card operations → references/gift-cards.md
├─ Custom receipt printing → references/receipts.md
├─ Activate, deactivate, update terminal → references/terminal-management.md
├─ Save a card for recurring charges → references/tokenization.md
└─ View transaction history or reprint receipt → references/transaction-history.md
```

## Start Here

1. **Setup**: Add Maven dependency, configure permissions, initialize `POSHandler`. See `references/setup.md`.
2. **Connect**: Discover and pair the terminal over Bluetooth, USB, or TCP/IP. See `references/connection.md`.
3. **Pay**: Initiate transactions through the connected terminal. See `references/payment.md`.
4. **Test with a Demo Debug POS device** — see `references/testing.md`.

## Non-Negotiable Rules

- **Card data is never exposed to your app.** The terminal encrypts all card data. Never attempt to capture or log card details.
- **Use a unique reference** for every transaction to prevent duplicates.
- **Check listener callbacks** for all results — operations are asynchronous.
- **Initialize `POSHandler` in your `Application` class** — not per-Activity.
- **Handle disconnection events** and reconnect gracefully — Bluetooth connections can drop.
- **Request a Demo Debug POS device** from the myPOS sales team for testing. Production terminals require an active merchant account and terminal registration.

## Implementation Workflow

1. Initialize `POSHandler` with currency and application context in `Application.onCreate()`.
2. Discover and connect to the terminal using `connectDevice()`.
3. Listen for connection events via `OnBindListener` or equivalent.
4. When connected, call the appropriate transaction method on `POSHandler.getInstance()`.
5. Receive results via the registered transaction listener callback.

## Required Response Contract

Every implementation must:

1. Show `POSHandler` initialization in the `Application` class.
2. Show terminal connection setup with the appropriate `ConnectionType`.
3. Show a complete transaction call with its listener callback.
4. Handle both success and failure in the listener.
5. State which connection type is being used and why.

## Validation Checklist

- `POSHandler` initialized once in `Application.onCreate()`.
- Bluetooth/USB/Network permissions declared in `AndroidManifest.xml`.
- Connection state handled before attempting a transaction.
- Transaction listener registered before calling the transaction method.
- Tested on a Demo Debug POS device with the myPOS Demo Card.

## References

Use `references/README.md` to pick the right reference file.
