---
name: mypos-smart-android
description: Guide for building Android apps that run directly on myPOS Smart terminal devices and integrate with the myPOS payment core via the Smart SDK. Use when implementing card payments, refunds, voids, pre-authorization, payment requests, gift cards, custom receipt printing, barcode scanning, or TWINT on myPOS Smart Android terminals.
license: Apache-2.0
---

# myPOS Smart SDK — Android

- Docs root: https://developers.mypos.com/
- SDK overview: https://developers.mypos.com/sdks/smart-sdk
- Integration context: https://developers.mypos.com/in-person-payments/paying-table

Use this skill to build Android apps that run **on** myPOS Smart terminal devices and trigger payments through the terminal's secure payment core via IPC.

## Quick Decision Tree

```
What operation do you need?
├─ Initiate a card payment → references/card-payment.md
├─ Refund or void a transaction → references/refunds-voids.md
├─ Hold funds, complete or cancel later → references/preauthorization.md
├─ Send a payment request via SMS or QR → references/payment-requests.md
├─ Gift card operations → references/gift-cards.md
├─ Custom receipt printing → references/receipts.md
├─ Scan a barcode or QR code → references/scanner.md
└─ TWINT / e-receipt / tipping / MOTO / installments / SAM → references/advanced.md
```

## Start Here

1. **Partner Portal + AppMarket**: Before development, understand how your app will be deployed to Smart devices. See `references/partner-portal.md`.
2. **Setup**: Add the Maven dependency, configure permissions, initialize IPC. See `references/setup.md`.
3. **Implement the payment**: The SDK uses a builder pattern — build a request, call `MyPOSAPI.openPaymentActivity()`, handle the result in `onActivityResult()`.
4. **Test with a Demo Debug POS device** — see `references/testing.md`.
5. **Upload APK and submit for review** before distribution to live devices.

## Non-Negotiable Rules

- **Card data is never exposed to your app.** All encryption happens in the certified terminal module. Never attempt to capture or log card details.
- **Use a unique `foreignTransactionId`** (UUID) for every payment to prevent duplicates.
- **Handle `onActivityResult()` correctly** — check both `resultCode` and the `status` extra to determine the payment outcome.
- **Do not cache or persist POS info** between sessions — always refresh via `MyPOSAPI.getPOSInfo()`.
- **APK must be uploaded to the Partner Portal** and pass the app review process before it can be distributed to Smart devices via AppMarket.
- **Request a Demo Debug POS device** from the myPOS sales team for testing. Standard production devices cannot run unsigned/development APKs.

## Implementation Workflow

1. Register in Partner Portal, understand the AppMarket deployment lifecycle.
2. Add Maven dependency and configure app permissions.
3. Bind to the myPOS service in your `Application` class.
4. Build the request object using the builder pattern.
5. Call `MyPOSAPI.openPaymentActivity()` with the request and a request code.
6. Override `onActivityResult()` in your Activity to receive the payment result.
7. Parse the result extras (`status`, `transaction_approved`, transaction details).

## Required Response Contract

Every implementation must:

1. Mention the Partner Portal registration and AppMarket upload requirement upfront.
2. Show Maven dependency in `build.gradle`.
3. Show the complete builder → `openPaymentActivity()` → `onActivityResult()` pattern.
4. Use `UUID.randomUUID().toString()` for `foreignTransactionId`.
5. Check `transaction_approved` and handle both success and failure cases.

## Validation Checklist

- Maven dependency added to `build.gradle`.
- Required permissions declared in `AndroidManifest.xml`.
- `foreignTransactionId` is unique per transaction (UUID).
- `onActivityResult()` checks both `resultCode == RESULT_OK` and `status == TRANSACTION_SUCCESS`.
- Tested on a Demo Debug POS device with the myPOS Demo Card.
- APK uploaded to Partner Portal and reviewed before production distribution.

## References

Use `references/README.md` to navigate to the right reference file.
