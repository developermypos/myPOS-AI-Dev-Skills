> Docs: https://developers.mypos.com/apis/checkout-api/pre-authorization
> Completion: https://developers.mypos.com/apis/checkout-api/pre-auth-completion
> Cancellation: https://developers.mypos.com/apis/checkout-api/pre-auth-cancellation
> Status: https://developers.mypos.com/apis/checkout-api/pre-auth-status

## Pre-Authorize (Hold Funds)

Server generates signed fields; customer approves the hold on the hosted page.

```ts
const fields = mypos.generatePreAuthFields(
  {
    amount: 100.00,
    currency: 'EUR',
  },
  `preauth-${Date.now()}`,
  {
    urlOk: 'https://example.com/preauth/held',
    urlCancel: 'https://example.com/preauth/cancel',
    urlNotify: 'https://example.com/api/mypos/notify',
  }
);
```

## Complete Pre-Authorization (Capture Funds)

```ts
const result = await mypos.completePreAuth({
  orderReference: preauthReference,
  amount: 85.00,   // can be less than the authorized amount
  currency: 'EUR',
});
```

## Cancel Pre-Authorization (Release Hold)

```ts
const result = await mypos.cancelPreAuth({
  orderReference: preauthReference,
});
```

## Get Pre-Authorization Status

```ts
const result = await mypos.getPreAuthStatus(preauthReference);
```

## Reading Order

1. `references/setup.md` — initialize first.
2. This file — pre-authorization flow.
3. `references/webhooks.md` — receive auth result via IPN.

## See Also

- references/webhooks.md
- references/stored-card-auth.md
