> Test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
> Test scenarios: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-scenarios
> Setup guide: https://developers.mypos.com/online-payments/initial-setup-and-testing

## Important: Real Cards Not Accepted in Sandbox

The sandbox environment **rejects real card numbers**. You must use the myPOS test cards listed at:
https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards

## Demo Merchant Account

Create a sandbox merchant account for testing:
https://merchant-demo.mypos.eu/en/login

This gives you a sandbox config pack to use during development.

## Sandbox Config Pack

```bash
# .env (development)
MYPOS_CONFIG_PACK=your-sandbox-config-pack
```

Initialize with `sandbox: true`:

```ts
const mypos = new MyPOSClient({
  configPack: process.env.MYPOS_CONFIG_PACK!,
  sandbox: true,
});
```

## Triggering Specific Test Scenarios

The test cards page documents which card numbers trigger:
- Successful payment
- 3DS challenge flow
- Declined payment
- Insufficient funds

## Going Live

1. Get a production config pack from the myPOS merchant portal
2. Set `sandbox: false` in the client initialization
3. Ensure `urlOk`, `urlCancel`, and `urlNotify` point to your production server

## Reading Order

1. `references/setup.md` — SDK initialization.
2. This file — sandbox setup.
3. `references/purchase.md` — first test payment.

## See Also

- references/setup.md
- references/purchase.md
