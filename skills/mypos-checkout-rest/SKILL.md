---
name: mypos-checkout-rest
description: Guide for integrating with the myPOS Checkout API directly over HTTP — no SDK required. Use when implementing payment flows in any language or stack: redirect checkout, server-side card charges, stored card authorization, pre-authorization, refunds, reversals, card tokenization, mandate-based transfers, or webhook handling. All requests require RSA-SHA256 signatures.
license: Apache-2.0
---

# myPOS Checkout API — Direct HTTP Integration

- Docs: https://developers.mypos.com/apis/checkout-api
- Getting started: https://developers.mypos.com/apis/checkout-api/checkout-getting-started

Use this skill to call the myPOS Checkout API directly from any language or stack. Card data never touches your server in redirect flows — the customer completes payment on the myPOS hosted page.

## Quick Decision Tree

```
What payment operation do you need?
│
├── Redirect customer to myPOS checkout page?
│   └── IPCPurchase → references/purchase.md
│
├── Charge a card server-to-server (no redirect)?
│   ├── Raw card details (PAN, CVC) → IPCIAPurchase → references/server-side-payment.md
│   └── Stored CardToken → IPCIAPurchase with CardToken → references/server-side-payment.md
│
├── Authorize a stored card, capture later?
│   └── IPCAuthorization → IPCAuthorizationCapture / IPCAuthorizationReverse
│       → references/authorization.md
│
├── Hold funds, complete or cancel later?
│   ├── Redirect flow → IPCPreAuthorization → references/preauthorization.md
│   └── In-app / server-side → IPCIAPreAuthorization → references/preauthorization.md
│
├── Refund or cancel a completed payment?
│   ├── Refund (partial or full) → IPCRefund → references/refund-reversal.md
│   └── Same-day cancel → IPCReversal → references/refund-reversal.md
│
├── Store a card for future charges?
│   ├── During redirect checkout → CardTokenRequest param → references/store-card.md
│   └── Directly in-app → IPCIAStoreCard / IPCIAStoredCardUpdate → references/store-card.md
│
├── Transfer funds between myPOS accounts?
│   ├── Push funds → IPCSendMoney → references/send-request-money.md
│   ├── Pull via mandate → IPCRequestMoney → references/send-request-money.md
│   └── Manage mandate → IPCMandateManagement → references/send-request-money.md
│
├── Create a payment session (for embedded checkout)?
│   └── IPCPaymentSessionCreate → references/payment-session.md
│
└── Handle payment notification / verify status?
    └── IPCPurchaseNotify webhook / IPCGetPaymentStatus → references/webhooks.md
```

## Start Here

1. **Get credentials**: Obtain your config pack (base64) or individual key files (private PEM, API public key) plus SID and WalletNumber from the myPOS merchant portal.
2. **Understand the signature scheme**: Every request requires an RSA-SHA256 `Signature` parameter. Read `references/authentication.md` before writing any request code.
3. **Set up your environment**: See `references/setup.md` for required parameter structure and key storage.
4. **Choose your operation**: Use the decision tree to select the right endpoint and reference file.
5. **Handle notifications**: Payment outcomes arrive via webhook. Read `references/webhooks.md` to validate them correctly.
6. **Test with test cards**: Sandbox rejects real cards. See `references/testing.md`.

## Non-Negotiable Rules

- **Private key must never be in client-side code, committed to version control, or served over HTTP.** Store it outside the web root or in a secrets manager.
- **Every request must include a valid RSA-SHA256 `Signature`.** Requests without a valid signature are rejected.
- **Every `IPCPurchaseNotify` webhook must be signature-verified before trusting.** Never update order status based on an unverified callback.
- **`IPCIAPurchase` with raw card data (PAN, CVC) requires PCI SAQ-D compliance.** If you are not PCI SAQ-D certified, use CardToken from a prior IPCPurchase or IPCIAStoreCard instead.
- **`OrderID` must be unique after a payment is marked as paid.** The API rejects duplicate OrderIDs for paid transactions.
- **Sandbox does not accept real cards.** Use official test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
- **Use `IPCGetPaymentStatus` or `IPCPurchaseNotify` to confirm payment** — never rely solely on the URL_OK redirect, which can be spoofed by the user.

## Implementation Workflow

1. Load private key and credentials from secure storage (never hardcode).
2. Build the request parameter map: `IPCmethod`, `IPCVersion=1.4`, `IPCLanguage`, `SID`, `WalletNumber`, `KeyIndex`, plus operation-specific fields.
3. Compute the `Signature`: sort all parameters by key name (case-sensitive), concatenate values, SHA-256 hash the string, RSA-PKCS#1 v1.5 sign with private key, Base64-encode.
4. POST all parameters (including `Signature`) to the appropriate endpoint.
5. For redirect flows: submit as an HTML form; the browser navigates to myPOS checkout.
6. For server-to-server flows: POST as form-encoded body; parse XML or JSON response; check `Status == 0`.
7. Receive and verify the `IPCPurchaseNotify` webhook; respond HTTP 200.
8. Optionally poll `IPCGetPaymentStatus` for a definitive status if the webhook is delayed.

## Required Response Contract

When helping implement this API, always:
- Show `authentication.md` first — signature computation is the foundation of every request
- Show the complete parameter map for the chosen operation (not just the unique fields)
- Show signature verification on the response/webhook side, not just the request side
- State clearly when PCI SAQ-D compliance is required (raw card data flows)
- Include `references/testing.md` for sandbox setup
- Reference the Checkout API docs URL for the specific endpoint

## Validation Checklist

- [ ] Private key stored outside web root and not in version control
- [ ] Signature computed correctly (sorted keys → concatenated values → SHA-256 → RSA sign → Base64)
- [ ] Response `Signature` verified after every API call
- [ ] `IPCPurchaseNotify` webhook signature verified before updating order status
- [ ] `OrderID` is unique per transaction
- [ ] Server responds HTTP 200 to webhook (no other status)
- [ ] Payment confirmed via webhook or `IPCGetPaymentStatus` before fulfilling order
- [ ] Sandbox tested with official test cards before going live

## References

See `references/README.md` for the full index and reading order.
