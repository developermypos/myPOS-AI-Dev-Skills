> Refund docs: https://developers.mypos.com/apis/checkout-api/refund
> Reversal docs: https://developers.mypos.com/apis/checkout-api/reversal
> SDK sample: https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/transaction-management

## Refund a Payment

Use when the original payment has been settled and funds need to be returned.

```ts
const result = await mypos.refund({
  orderReference: originalOrderReference,
  amount: 25.00,       // partial refund supported
  currency: 'EUR',
  reason: 'Customer return',
});
```

## Reverse a Payment

Use when the payment has not yet settled (same-day cancellation).

```ts
const result = await mypos.reversal({
  orderReference: originalOrderReference,
});
```

## Reading Order

1. `references/purchase.md` — the original payment you are refunding/reversing.
2. This file — refund or reverse.

## See Also

- references/purchase.md
- https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/transaction-management
