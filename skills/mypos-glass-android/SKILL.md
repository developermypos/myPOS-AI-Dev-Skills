---
name: mypos-glass-android
description: Guide for accepting NFC card-present payments through the myPOS Glass Soft POS application on standard Android devices. Use when building an Android app or web page that triggers contactless payments via myPOS Glass, including card payments, refunds, voids, payment requests, and deep links from web pages or external apps.
license: Apache-2.0
---

# myPOS Glass SDK — Android Soft POS

- Docs root: https://developers.mypos.com/
- SDK overview: https://developers.mypos.com/sdks/glass-sdk
- Integration kit: https://developers.mypos.com/guides/integration-kits/glass-integration

Use this skill to accept NFC card-present payments through the myPOS Glass application — a Soft POS solution that turns any NFC-enabled Android device into a payment terminal. The Glass app handles all card security; your app or web page triggers the payment.

## Quick Decision Tree

```
How are you triggering the payment?
├─ Native Android app with Glass SDK → use the SDK
│  ├─ Card payment → references/card-payment.md
│  ├─ Refund or void → references/refunds-voids.md
│  ├─ Payment request (QR/SMS/email) → references/payment-requests.md
│  ├─ Custom receipt or e-receipt → references/receipts.md
│  └─ Get last transaction data → references/card-payment.md (see also section)
└─ Web page or external app → use deep links
   └─ references/deep-links.md
```

## Start Here

1. **Setup**: The myPOS Glass app must be installed on the target device. Add the Maven dependency. See `references/setup.md`.
2. **Choose your integration path**: Native Android SDK or deep links.
3. **Test on supported hardware** — NFC-enabled Android device with myPOS Glass installed. See `references/testing.md`.

## Non-Negotiable Rules

- **The myPOS Glass app must be installed** on the device before your integration can work. This is a hard requirement.
- **Card data is never exposed to your app.** The Glass app handles all NFC and card security.
- **Only Visa and Mastercard are supported.** Unlike Smart SDK, Glass does not support UnionPay, JCB, or Bancontact.
- **Use a unique `foreignTransactionId`** (UUID) for every payment.
- **Deep links from web pages do not require the SDK** — they open the Glass app directly via a URI scheme.

## Implementation Workflow

**Native SDK path:**
1. Verify myPOS Glass app is installed on the device.
2. Add Maven dependency and configure permissions.
3. Build the request using the builder pattern.
4. Call `MyPOSAPI.openPaymentActivity()`.
5. Receive result in `onActivityResult()`.

**Deep link path:**
1. Construct the payment URI with amount, currency, and your callback URL.
2. Open the URI via `Intent.ACTION_VIEW` or an `<a>` tag.
3. Glass app handles payment; result is returned via the callback scheme.

## Required Response Contract

Every implementation must:

1. State whether the integration is native SDK or deep link.
2. For native: show Maven dependency and the builder → `openPaymentActivity()` → `onActivityResult()` pattern.
3. For deep links: show the complete URI format with all required parameters.
4. Note the Visa/Mastercard-only restriction.
5. Reference `references/testing.md` for device requirements.

## Validation Checklist

- myPOS Glass app installed on test device.
- `foreignTransactionId` is unique per transaction (UUID).
- `onActivityResult()` handles both success and failure.
- Tested on an NFC-enabled Android device.
- Visa and Mastercard tested; no other card schemes expected.

## References

Use `references/README.md` to pick the right reference file.
