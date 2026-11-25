---
name: mypos-checkout-embedded
description: Use when a developer needs to embed a myPOS payment form directly in their website without redirecting customers to a hosted checkout page
license: Apache-2.0
---

# myPOS Embedded Checkout SDK

> Docs: https://developers.mypos.com/sdks/embedded-sdk
> Sample code: https://developers.mypos.com/sdks/embedded-sdk/sample-code

Use this skill to integrate a secure iFrame payment form directly into your website. The customer never leaves your page — the card form renders inline.

**Supported payment methods:** Mastercard, Maestro, Visa, Visa Electron, VPAY, JCB, Bancontact, iDEAL (Netherlands only), Apple Pay, Google Pay.

## Quick Decision Tree

```
Embedded payment form on your site (no redirect)?
│
├── Card payment (embedded form)?
│   ├── SDK-rendered Pay button → createPayment()
│   │   → references/card-payment.md
│   └── Your own Pay button → createPaymentForm()
│       → references/deferred-payment.md
│
├── Apple Pay or Google Pay button?
│   └── createApplePayButton() / createGooglePayButton()
│       (server-side session token required first)
│       → references/apple-google-pay.md
│
├── Customize the form's appearance?
│   └── Pass customization object as 4th argument
│       → references/customization.md
│
└── Handle server-side payment notification?
    └── urlNotify webhook
        → references/webhooks.md
```

## Start Here

1. **Install or include** the SDK via npm or CDN script tag. See `references/setup.md`.
2. **Add a container div** in your HTML — minimum 320×620px.
3. **Build `paymentParams`** with your SID, wallet number, amount, currency, order ID, cart items, and callback URLs.
4. **Build `callbackParams`** with `isSandbox: true` and `onSuccess` / `onError` handlers.
5. Call `createPayment()` for the simplest integration, or `createPaymentForm()` if you need a custom Pay button.
6. **Verify the payment server-side** after `onSuccess` — see `references/webhooks.md`.
7. For Apple/Google Pay, read `references/apple-google-pay.md` — it requires additional server-side and account setup.

## Non-Negotiable Rules

- **iFrame container must be at least 320×620px.** Smaller containers clip or break the form.
- **`urlNotify` must use HTTPS.** Plain HTTP is rejected. Your server must respond HTTP 200 with body `"OK"` — any other response triggers myPOS retry or error handling.
- **`onSuccess` is not proof of payment.** Always verify server-side via `IPCGetPaymentStatus` before fulfilling any order or subscription.
- **Apple Pay requires two distinct setup steps:** (1) host the domain verification file at `/.well-known/apple-developer-merchantid-domain-association` and (2) register the domain in your myPOS account under Store Settings.
- **Use `isSandbox: true` for all development and testing.** Never set `false` in a non-production environment.
- **Sandbox does NOT accept real cards.** Use the official test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
- **Order IDs must be unique** per transaction.

## Implementation Workflow

### Card Payment (Standard)
1. Add `<div id="myPOSEmbeddedCheckout"></div>` to your page (min 320×620px)
2. Import or load the SDK
3. Build `paymentParams` (sid, walletNumber, amount, currency, orderID, urlNotify, cartItems)
4. Build `callbackParams` (isSandbox, onSuccess, onError)
5. Call `MyPOSEmbedded.createPayment('myPOSEmbeddedCheckout', paymentParams, callbackParams)`
6. In `onSuccess`: call your server to verify via `IPCGetPaymentStatus`

### Card Payment (Custom Button)
Same setup, but call `createPaymentForm()` instead — it returns a Promise resolving to a `payment` object. Attach `payment.processPayment()` to your button's click handler.

### Apple / Google Pay
1. Server calls `PaymentSessionCreate` API → gets `sessionToken`
2. Pass `sessionToken` to client
3. Check `IS_APPLE_PAY_AVAILABLE` and call the appropriate `create*Button()` method
4. Apple Pay only: complete domain verification and myPOS account setup first

## Required Response Contract

When helping implement this SDK, always:
- Show both `paymentParams` and `callbackParams` objects in full
- Include the container div with an explicit size comment (min 320×620px)
- Show `isSandbox: true` in `callbackParams`
- Include a comment in `onSuccess` reminding the developer to verify server-side
- For Apple/Google Pay: show the `IS_APPLE_PAY_AVAILABLE` branch and note both Apple Pay setup steps

## Validation Checklist

- [ ] Container div present and at least 320×620px
- [ ] `isSandbox: true` during development
- [ ] `urlNotify` uses HTTPS
- [ ] Server responds HTTP 200 + `"OK"` to IPN
- [ ] `onSuccess` triggers server-side `IPCGetPaymentStatus` verification
- [ ] Order IDs are unique per transaction
- [ ] Apple Pay: domain file hosted + myPOS account configured (if using Apple Pay)
- [ ] Tested with sandbox test cards before going live

## References

See `references/README.md` for the full index and reading order.
