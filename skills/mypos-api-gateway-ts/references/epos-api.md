> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/sample-code/epos
> API reference: https://developers.mypos.com/apis/epos

## Create a POS Payment

```ts
const result = await client.epos.payments.create({
  terminalId: 'TID12345',
  amount: 15.00,
  currency: 'EUR',
  reference: `order-${Date.now()}`,
});
if (result.success) {
  console.log('Payment ID:', result.data.paymentId);
}
```

## Get Payment Status

```ts
const result = await client.epos.payments.getById(paymentId);
if (result.success) {
  console.log('Status:', result.data.status);
}
```

## Reverse a Payment

```ts
const result = await client.epos.payments.reverse(paymentId);
```

## Create a Refund

```ts
const result = await client.epos.payments.refund({
  paymentId,
  amount: 15.00,
  reason: 'Customer return',
});
```

## List Terminals

```ts
const result = await client.epos.terminals.list();
if (result.success) {
  const activeTerminals = result.data.terminals.filter(t => t.status === 'ACTIVE');
}
```

## Terminal Activation / Deactivation

```ts
// Get activation code
const activationResult = await client.epos.terminals.getActivationCode(terminalId);

// Deactivate
const deactivationResult = await client.epos.terminals.getDeactivationCode(terminalId);
```

## API Endpoints

| Operation | URL |
|-----------|-----|
| Create payment | https://developers.mypos.com/apis/epos/payment/epos-create-payment |
| Get payment | https://developers.mypos.com/apis/epos/payment/epos-get-payment-by-id |
| Reverse payment | https://developers.mypos.com/apis/epos/payment/epos-reversal-by-payment-id |
| Create refund | https://developers.mypos.com/apis/epos/payment/epos-create-refund |
| List terminals | https://developers.mypos.com/apis/epos/terminal/epos-get-terminals |
| Terminal activation code | https://developers.mypos.com/apis/epos/terminal/epos-terminal-activation-code |

## Reading Order

1. `references/authentication.md` — initialize SDK first.
2. This file — ePOS API calls.
3. `references/error-handling.md` — handle errors.

## See Also

- references/authentication.md
- references/error-handling.md
