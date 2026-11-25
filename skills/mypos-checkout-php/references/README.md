# myPOS Online Checkout PHP SDK — References Index

> Docs: https://developers.mypos.com/online-payments/sdks/php-sdk

## Reading Order

1. `references/setup.md` — **Start here.** Composer install, Config initialisation.
2. Open the reference matching your payment flow.
3. `references/webhooks.md` — Always implement IPN handling.
4. `references/testing.md` — Sandbox and test cards.

## All References

| File | Use when... |
|------|-------------|
| `setup.md` | First-time SDK setup and Config object |
| `purchase.md` | Redirect customer to myPOS checkout page |
| `server-side-payment.md` | Charge card directly (IAPurchase / IAPurchaseWithToken) |
| `store-card.md` | Tokenize a card for reuse |
| `refund-reversal.md` | Refund or reverse a payment |
| `preauthorization.md` | Pre-authorize (hold funds), then complete or cancel |
| `authorization.md` | Server-side authorization + capture (direct) |
| `transfer-of-funds.md` | Mandates, send money, request money |
| `webhooks.md` | Receive and validate IPN callbacks |
| `testing.md` | Sandbox, test cards, Demo Merchant Account |

## See Also

- https://developers.mypos.com/online-payments/initial-setup-and-testing
