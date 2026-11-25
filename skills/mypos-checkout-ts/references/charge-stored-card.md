> Docs: https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/stored-cards

## Charge a Stored Card

Server-to-server — no browser form needed. Requires a `cardToken` from a previous Store Card flow.

```ts
const result = await mypos.chargeStoredCard({
  cardToken: storedCardToken,        // from Store Card IPN callback
  amount: 29.99,
  currency: 'EUR',
  orderReference: `order-${Date.now()}`,
  description: 'Monthly subscription',
});

if (result.success) {
  console.log('Charged:', result.transactionId);
} else {
  console.error('Charge failed:', result.message);
}
```

This is a direct server-to-server call — the customer does not need to interact.

## Reading Order

1. `references/store-card.md` — obtain the card token first.
2. This file — charge the stored card.
3. `references/refund-reversal.md` — refund if needed.

## See Also

- references/store-card.md
- references/refund-reversal.md
- https://developers.mypos.com/apis/checkout-api/get-payment-status
