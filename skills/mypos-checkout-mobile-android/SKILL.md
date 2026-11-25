---
name: mypos-checkout-mobile-android
description: Build in-app card payments into a native Android app with the myPOS Mobile Checkout SDK
license: Apache-2.0
---

# myPOS Mobile Checkout — Android SDK

- Docs root: https://developers.mypos.com/in-app-payments/android-payments
- SDK overview: https://developers.mypos.com/in-app-payments/android-payments

Use this skill to accept card payments **inside your own native Android app** — no redirect to a
browser or hosted page. Card entry happens in the SDK's own Activities; your app never sees raw
card data.

## Quick Decision Tree

```
What operation do you need?
├─ One-time card payment → references/purchase.md
├─ Charge a previously stored card → references/purchase.md (stored-card section)
├─ Save or update a card for later use → references/store-card.md
├─ Hold funds now, capture or cancel later → references/preauthorization.md
├─ Refund a completed payment → references/refund.md
├─ Check the status of a Purchase/Refund → references/transaction-status.md
├─ Match the payment screens to your app's branding → references/ui-customization.md
└─ First time integrating → references/setup.md, then references/testing.md
```

## Start Here

1. **Get credentials**: Obtain either a `configurationKey` (Easy Setup, production) or a
   SID/Wallet Number/key pair (Advanced Setup, sandbox) from the myPOS merchant portal/dashboard.
   See `references/setup.md`.
2. **Add the SDK**: Maven Central coordinate `com.mypos:mobilepaymentssdk:1.2.8`. Do not use the
   `jcenter()`/`checkoutsdk` coordinate found in older material — JCenter has been shut down.
3. **Initialize once**: `MyPos.getInstance().init(...)` with `isSandbox=true` during development.
4. **Launch the right Activity** for the operation (decision tree above) with a unique Order ID,
   and read the result in `onActivityResult`.
5. **Test in sandbox**, then email `integrations@mypos.com` to request production app review
   before going live. See `references/testing.md`.

## Non-Negotiable Rules

- **Card entry happens entirely inside the SDK's own Activities** (`PurchaseActivity`,
  `StoreCardActivity`, `UpdateCardActivity`, `RefundActivity`, `PreAuthorizationActivity`). Never
  build a custom card-entry form and pass raw PAN/CVV into the SDK.
- **Never describe the payment gateway as "iCard" or "Intercard Finance AD."** Older material uses
  this legacy name; myPOS no longer works with iCard. Always say "myPOS."
- **Install via Maven Central** (`com.mypos:mobilepaymentssdk:1.2.8`, `mavenCentral()`). The
  `jcenter()` / `com.mypos:checkoutsdk:1.2.2` coordinate seen in some older material is dead.
- **Credentials come from the myPOS merchant portal/dashboard.** Never hardcode a production
  `configurationKey`, SID, wallet number, or private/public key in source — read from environment
  configuration or a secrets store.
- **Every Purchase, Store Card, and Pre-Authorization call needs a unique Order ID / Reference ID.**
  Reusing one will be rejected or will collide with a prior transaction.
- **Use `isSandbox=true` during development.** Production traffic requires myPOS's approval —
  email `integrations@mypos.com` and complete app review before going live.
- **Always check `INTENT_EXTRA_STATUS == MyPos.STATUS_SUCCESS` in `onActivityResult`** before
  reading `INTENT_EXTRA_TRANSACTION_REFERENCE` or any other result extra. A non-cancelled result
  code does not by itself mean the payment succeeded.
- **Pre-Authorization requires SDK `1.2.8`+.** If `PreAuthorizationActivity`,
  `PreAuthCompleteOrCancelActivity`, or `IPCPreAuthStatus` don't resolve, confirm the Maven Central
  version on the classpath — older bundled builds may predate this feature.
- **There is no webhook/IPN for this SDK.** Results arrive only via `onActivityResult`. For
  authoritative confirmation before fulfilling a high-value order, call `IPCGetTransactionStatus`
  rather than trusting on-device state alone.

## Implementation Workflow

1. Obtain sandbox credentials from the myPOS merchant portal.
2. Add the Maven Central dependency (`com.mypos:mobilepaymentssdk:1.2.8`).
3. Initialize `MyPos.getInstance()` once (Easy Setup with a `configurationKey`, or Advanced Setup
   with SID/Wallet Number/keys) with `isSandbox=true`; call `setLanguage`/`setKeyIndex` as needed.
4. Launch the relevant Activity (`PurchaseActivity`, `StoreCardActivity`, `PreAuthorizationActivity`,
   `RefundActivity`, ...) via `startActivityForResult` with a unique Order ID.
5. Handle the result in `onActivityResult`: check `INTENT_EXTRA_STATUS` first, then read the
   operation-specific extras.
6. Optionally call `IPCGetTransactionStatus` for authoritative confirmation.
7. Apply `Theme.MyPOS` overrides if custom branding is needed.
8. Test end-to-end in sandbox, then request production approval via `integrations@mypos.com`.

## Required Response Contract

Every implementation must:

1. State which operation is being implemented and which Activity/class it maps to.
2. Show the Maven Central dependency (`com.mypos:mobilepaymentssdk:1.2.8`), never the dead JCenter one.
3. Show credentials read from configuration/secrets, not hardcoded.
4. Show a unique Order ID generated per call (e.g. `UUID.randomUUID().toString()`).
5. Show the `INTENT_EXTRA_STATUS` check before treating a result as successful.
6. Reference the matching https://developers.mypos.com/in-app-payments/android-payments/... page.
7. Never use the words "iCard" or "Intercard."

## Validation Checklist

- Maven Central dependency is `com.mypos:mobilepaymentssdk:1.2.8` (or newer), not `checkoutsdk`/JCenter.
- Credentials are read from environment/secrets configuration, not hardcoded.
- `isSandbox=true` during development; sandbox tested before requesting production approval.
- Every Purchase/Store Card/Pre-Authorization call uses a unique Order ID.
- `onActivityResult` checks `INTENT_EXTRA_STATUS == MyPos.STATUS_SUCCESS` before reading other extras.
- No "iCard"/"Intercard" wording anywhere in the generated code or explanation.
- Tested both a successful payment and a declined/cancelled case.

## References

Use `references/README.md` to navigate to the right reference file.
