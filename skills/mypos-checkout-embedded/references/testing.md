> Testing guide: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox
> Test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards

## Enable Sandbox Mode

Set `isSandbox: true` in your `callbackParams`:

```javascript
const callbackParams = {
    isSandbox: true,   // ← always true during development
    onSuccess: (data) => { ... },
    onError: () => { ... }
};
```

## Test Cards

**The sandbox DOES NOT accept real credit/debit cards.**
Use the official test cards listed at:
https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards

Example test cards:

| Card | Number | Expiry | CVV | Result |
|------|--------|--------|-----|--------|
| Visa | 4111 1111 1111 1111 | 12/26 | 123 | Success |
| Mastercard | 5500 0000 0000 0004 | 12/26 | 123 | Success |

See the full list and decline/3DS scenario cards at the URL above.

## Demo Merchant Account

Create and manage a sandbox merchant account:
https://merchant-demo.mypos.eu/en/login

## Testing the urlNotify Webhook Locally

The `urlNotify` endpoint must be reachable from the internet. Use a tunnel tool (e.g. ngrok) during local development:

```bash
ngrok http 3000
# Use the https:// ngrok URL as urlNotify
```

## Going Live Checklist

- [ ] Change `isSandbox: false` in `callbackParams`
- [ ] Replace sandbox SID and wallet number with production credentials
- [ ] Switch `urlNotify` to production HTTPS endpoint
- [ ] Complete Apple Pay domain verification and myPOS account setup (if using Apple Pay)
- [ ] Run a real low-value test transaction on production before going live

## Reading Order

1. `references/setup.md` — SDK setup.
2. `references/card-payment.md` — build the payment form.
3. This file — sandbox and test cards.

## See Also

- references/webhooks.md
