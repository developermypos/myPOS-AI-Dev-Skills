> Testing guide: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox
> Test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
> Test scenarios: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-scenarios

## Sandbox Environment

Switch to sandbox by setting the IPC URL on the Config:

```php
$config->setIpcURL('https://www.mypos.eu/vmp/checkout-test');
```

Or use the Demo Merchant Portal to get sandbox credentials:
https://merchant-demo.mypos.eu/en/login

## Test Cards

**The sandbox DOES NOT accept real credit/debit cards.**
Use the official test cards listed at:
https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards

Example test cards:

| Card | Number | Expiry | CVV | Result |
|------|--------|--------|-----|--------|
| Visa | 4111 1111 1111 1111 | 12/26 | 123 | Success |
| Mastercard | 5500 0000 0000 0004 | 12/26 | 123 | Success |

See the full list and decline-scenario cards at the link above.

## Testing Webhooks Locally

Use a tunnel tool (e.g. ngrok) to expose your local `urlNotify` endpoint to the internet so the myPOS sandbox can reach it.

## Reading Order

1. `references/setup.md` — Config with sandbox URL.
2. `references/purchase.md` — run a test redirect checkout.
3. This file — test card reference.

## See Also

- references/webhooks.md
- https://developers.mypos.com/online-payments/initial-setup-and-testing
