> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/sample-code/ecommerce
> API reference: https://developers.mypos.com/apis/ecommerce

## Create a Payment Link

```ts
const result = await client.ecommerce.paymentLinks.create({
  amount: 25.00,
  currency: 'EUR',
  description: 'Invoice #1001',
  expiresAt: '2024-12-31T23:59:59Z',
});
if (result.success) {
  console.log('Payment link URL:', result.data.url);
}
```

## List Payment Links

```ts
// listAll() auto-paginates and returns all results
const result = await client.ecommerce.paymentLinks.listAll();
if (result.success) {
  console.log(result.data.links);
}
```

## Create a Payment Button

```ts
const result = await client.ecommerce.paymentButtons.create({
  amount: 10.00,
  currency: 'EUR',
  description: 'Donate',
});
```

## Send a Payment Request

```ts
const result = await client.ecommerce.paymentRequests.create({
  amount: 50.00,
  currency: 'EUR',
  recipientEmail: 'customer@example.com',
  description: 'Order #2001',
});
```

## API Endpoints

| Operation | URL |
|-----------|-----|
| Payment links | https://developers.mypos.com/apis/ecommerce/link/create-payment-link |
| Payment buttons | https://developers.mypos.com/apis/ecommerce/button/create-payment-button |
| Payment requests | https://developers.mypos.com/apis/ecommerce/payment-request/create-payment-request |

## Reading Order

1. `references/authentication.md` — initialize SDK first.
2. This file — Ecommerce API calls.
3. `references/error-handling.md` — handle errors.

## See Also

- references/authentication.md
- references/error-handling.md
