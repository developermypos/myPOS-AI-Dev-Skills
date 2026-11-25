> Docs: https://developers.mypos.com/apis/checkout-api
> Getting started: https://developers.mypos.com/apis/checkout-api/checkout-getting-started

## Reading Order

1. `references/setup.md` — credentials, required parameters for every request
2. `references/authentication.md` — RSA-SHA256 signature computation (read before writing any code)
3. The reference file for your operation (see table below)
4. `references/webhooks.md` — IPN notification handling (required for all payment flows)
5. `references/testing.md` — sandbox setup and test cards

## All References

| File | Use when... |
|------|-------------|
| `setup.md` | Setting up credentials and understanding the common request structure |
| `authentication.md` | Computing the RSA-SHA256 `Signature` parameter required on every request |
| `purchase.md` | Redirect customer to myPOS hosted checkout (IPCPurchase) |
| `server-side-payment.md` | Charge a card server-to-server without redirect (IPCIAPurchase) |
| `authorization.md` | Authorize a stored card now, capture or void later |
| `preauthorization.md` | Hold funds with or without customer redirect; complete or cancel later |
| `refund-reversal.md` | Refund a completed payment or cancel it same-day |
| `store-card.md` | Save a card token during checkout or directly in-app; update stored card details |
| `send-request-money.md` | Transfer funds between myPOS accounts; manage recurring mandates |
| `payment-session.md` | Create a payment session token for embedded checkout flows |
| `webhooks.md` | Receive and verify IPCPurchaseNotify; poll IPCGetPaymentStatus |
| `testing.md` | Sandbox environment setup and test card numbers |
