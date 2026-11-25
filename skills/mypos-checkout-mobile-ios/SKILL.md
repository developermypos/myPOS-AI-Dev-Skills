---
name: mypos-checkout-mobile-ios
description: Build in-app card payments into a native iOS app with the myPOS Mobile Checkout SDK
license: Apache-2.0
---

# myPOS Mobile Checkout — iOS SDK

- Docs root: https://developers.mypos.com/in-app-payments/ios-payments
- SDK overview: https://developers.mypos.com/in-app-payments/ios-payments

Use this skill to accept card payments **inside your own native iOS app** — no redirect to a
browser or hosted page. Card entry happens in the SDK's own view controllers; your app never sees
raw card data.

## Quick Decision Tree

```
What operation do you need?
├─ One-time card payment → references/purchase.md
├─ Charge a previously stored card → references/purchase.md (stored-card overload)
├─ Save or update a stored card → references/store-card.md
├─ Refund a completed payment → references/refund.md
├─ Check the status of a Purchase/Refund → references/transaction-status.md
├─ Match the payment screens to your app's branding → references/ui-customization.md
├─ Pre-authorization / authorization / reversal (advanced, not yet on public docs) → references/advanced.md
└─ First time integrating → references/setup.md, then references/testing.md
```

## Start Here

1. **Get credentials**: Obtain either a `configurationKey` (Easy Setup, production) or an
   account number/store ID/certificate/private-key pair (Advanced Setup, sandbox) from the myPOS
   merchant portal/dashboard. See `references/setup.md`.
2. **Embed the SDK**: Drag `MPCheckout.xcframework` into your project ("Embed & Sign"). There is no
   CocoaPods, Swift Package Manager, or Carthage distribution — manual embedding only.
3. **Initialize once**, ideally in `AppDelegate`, with `isSandbox: true` during development.
4. **Present the right view controller** for the operation (decision tree above) with a unique
   order ID, and handle the result in its delegate/completion block.
5. **Test in sandbox**, then email `integrations@mypos.com` to request production app review
   before going live. See `references/testing.md`.

## Non-Negotiable Rules

- **Card entry happens entirely inside the SDK's own view controllers**
  (`MPPaymentViewController`, `MPStoreCardViewController`, `MPUpdateStoredCardViewController`).
  Never build a custom card-entry form and pass raw PAN/CVV into the SDK.
- **Never describe the payment gateway as "iCard" or "Intercard Finance AD."** Older material uses
  this legacy name; myPOS no longer works with iCard. Always say "myPOS."
- **No CocoaPods/SPM/Carthage exists for this SDK.** `MPCheckout.xcframework` must be manually
  embedded ("Embed & Sign" in Frameworks, Libraries, and Embedded Content). The framework's actual
  `MinimumOSVersion` is 11.0.
- **No bridging header is required in Swift.** The framework ships with a module map, so
  `import MPCheckout` works directly — unlike the Terminal iOS SDK skill's Objective-C-only framework.
- **Prefer Easy Setup** (`MPCheckout.initialize(in:withConfigurationKey:...)`, config-pack based)
  for production. Advanced Setup (certificate/private-key files bundled via Copy Bundle Resources)
  is for sandbox/testing or accounts provisioned only with certificate/key credentials.
- **The public docs sometimes label these same APIs `MobilePaymentSDK`/`PaymentViewController`/
  `StoredCard`/`MobilePaymentSDKError`** instead of `MPCheckout`/`MPPaymentViewController`/
  `MPStoredCard`/`MPCheckoutError`. Same functionality, different naming depending on SDK version —
  this skill uses the names confirmed in the compiled framework.
- **Generate real unique order IDs** (`UUID().uuidString` in Swift, `[[NSUUID UUID] UUIDString]` in
  Objective-C). Do not reference `ICUtils.randomOrderId` from older sample code — it is not a real
  symbol in the current SDK.
- **Always check for a non-nil error** in every delegate/completion callback before treating a
  result as successful.
- **There is no webhook/IPN for this SDK.** Results arrive only via delegate/completion callbacks.
  For authoritative confirmation before fulfilling a high-value order, call
  `MPCheckout.getOrderStatus(...)` rather than trusting on-device state alone.
- **Pre-Authorization, Authorization, and Reversal (`references/advanced.md`) are confirmed present
  in the compiled framework's headers but are not covered in the public iOS SDK docs.** Verify
  behavior in sandbox before relying on them in production.

## Implementation Workflow

1. Obtain sandbox credentials/configuration key from the myPOS merchant portal.
2. Drag `MPCheckout.xcframework` into the project (Embed & Sign).
3. Call `MPCheckout.initialize(...)` once at app launch (Easy Setup or Advanced Setup) with
   `isSandbox: true`.
4. Present the relevant `MPCheckoutController` subclass modally with a unique order ID and the
   matching delegate.
5. Handle the delegate/completion callback: check for an error before reading the success payload.
6. Optionally call `MPCheckout.getOrderStatus(...)` for authoritative confirmation.
7. Apply `MPCheckoutTheme` via `MPCheckout.applyTheme(...)` if custom branding is needed.
8. Test end-to-end in sandbox, then request production approval via `integrations@mypos.com`.

## Required Response Contract

Every implementation must:

1. State which operation is being implemented and which view controller/method it maps to.
2. Show both Swift and Objective-C where the operation differs meaningfully between them.
3. Show credentials read from configuration/secrets, not hardcoded.
4. Show a unique order ID generated per call (`UUID().uuidString`), never `ICUtils.randomOrderId`.
5. Show the error check in the delegate/completion callback before treating a result as successful.
6. Reference the matching https://developers.mypos.com/in-app-payments/ios-payments/... page, or —
   for advanced operations not on the public docs — say so explicitly and point to the framework
   header as the source of truth.
7. Never use the words "iCard" or "Intercard."

## Validation Checklist

- `MPCheckout.xcframework` is embedded with "Embed & Sign," not referenced via a package manager
  that doesn't actually support it.
- Credentials are read from environment/secrets configuration, not hardcoded.
- `isSandbox: true` during development; sandbox tested before requesting production approval.
- Every Purchase/Store Card call uses a unique order ID (`UUID().uuidString`), not `ICUtils.randomOrderId`.
- Every delegate/completion callback checks for an error before reading success data.
- No "iCard"/"Intercard" wording anywhere in the generated code or explanation.
- Tested both a successful payment and a declined/cancelled case.

## References

Use `references/README.md` to navigate to the right reference file.
