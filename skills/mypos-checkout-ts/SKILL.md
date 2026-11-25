---
name: mypos-checkout-ts
description: Guide for accepting card payments on websites using the myPOS Online Checkout SDK. Use when implementing purchase flows, stored card tokenization, recurring charges, refunds, reversals, pre-authorization, stored card authorization, or transfer of funds via myPOS hosted checkout forms with server-side HMAC-signed requests.
license: Apache-2.0
---

# myPOS Online Checkout

- Docs root: https://developers.mypos.com/
- SDK overview: https://developers.mypos.com/sdks/online-checkout-typescript-sdk
- Checkout API: https://developers.mypos.com/apis/checkout-api

Use this skill to accept card payments on a website with minimal PCI scope. Card data never touches your server — payments are processed through myPOS hosted checkout forms.

## Quick Decision Tree

```
What payment operation do you need?
├─ Accept a card payment → Purchase
│  └─ references/purchase.md
├─ Save a card for later use → Store Card
│  └─ references/store-card.md
├─ Charge a previously saved card → Charge Stored Card
│  └─ references/charge-stored-card.md
├─ Refund or reverse a completed payment → Refund / Reversal
│  └─ references/refund-reversal.md
├─ Hold funds, complete or cancel later → Pre-authorization
│  └─ references/preauthorization.md
├─ Authorize a stored card (capture later) → Stored Card Authorization
│  └─ references/stored-card-auth.md
├─ Send or request money between accounts → Transfer of Funds
│  └─ references/transfer-of-funds.md
└─ Handle payment notifications from myPOS → Webhooks / IPN
   └─ references/webhooks.md
```

## Start Here

1. **Initial setup**: Get a config pack (base64 string) from the myPOS merchant portal and initialize the SDK. See `references/setup.md`.
2. **Choose payment type**: Pick the flow from the decision tree above.
3. **Server signs, client submits**: All payment forms are generated server-side with an HMAC signature, then submitted client-side through the myPOS hosted form.
4. **Handle the IPN callback**: myPOS sends a POST notification to your server after payment. Validate it. See `references/webhooks.md`.
5. **Test with test cards**: The sandbox does not accept real cards. See `references/testing.md`.

## Non-Negotiable Rules

- **The config pack and private key are server-side only.** Never expose them in client-side code or version control.
- **All form fields must be signed server-side** using HMAC-SHA256 before being sent to the client.
- **Validate every IPN callback** — do not trust payment success notifications without verifying the signature.
- **Unique order reference per transaction.** Never reuse an order reference; use UUID or order ID.
- **Sandbox uses test cards only.** Real cards are rejected in the sandbox environment. Use the test cards listed at https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards.
- **Set both `urlOk` and `urlCancel`** redirect URLs in every payment request.

## Implementation Workflow

1. Initialize `MyPOSClient` from the config pack on the server.
2. Server calls `generatePurchaseFields()` (or equivalent) with order details — this signs the request.
3. Pass the signed fields to the client-side `<PaymentForm>` component.
4. The browser POSTs the form to the myPOS hosted checkout URL.
5. myPOS processes the payment and sends an IPN POST to your server.
6. Your server validates the IPN and updates the order status.
7. The customer is redirected to `urlOk` or `urlCancel`.

## Required Response Contract

Every implementation must:

1. Show server-side initialization from the config pack (never hardcoded).
2. Show signed field generation on the server.
3. Show the `<PaymentForm>` component or equivalent form submission on the client.
4. Show IPN validation using `validateWebhook()`.
5. Reference the sandbox test card URL for testing instructions.

## Validation Checklist

- Config pack is in an environment variable, not hardcoded.
- Every order uses a unique reference.
- IPN handler validates the signature before updating order status.
- Test payment completed using sandbox test cards before going live.
- `urlOk` and `urlCancel` redirect URLs are set.
- Tested both successful payment and deliberate failure case.

## References

Use `references/README.md` to pick the right reference file.
