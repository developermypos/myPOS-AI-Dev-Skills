> Docs: https://developers.mypos.com/apis/checkout-api/authorization
> Capture: https://developers.mypos.com/apis/checkout-api/authorization-capture
> Reverse: https://developers.mypos.com/apis/checkout-api/authorization-reverse
> List: https://developers.mypos.com/apis/checkout-api/authorization-list
> SDK sample: https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/stored-cards

## Authorize a Stored Card (Hold Funds)

Server-to-server call — no browser form needed. Requires a `cardToken`.

```ts
const result = await mypos.authorize({
  cardToken: storedCardToken,
  amount: 150.00,
  currency: 'EUR',
  orderReference: `auth-${Date.now()}`,
});
```

## Capture (Finalize the Charge)

```ts
const result = await mypos.captureAuthorization({
  orderReference: authReference,
  amount: 150.00,
  currency: 'EUR',
});
```

## Reverse (Cancel the Hold)

```ts
const result = await mypos.reverseAuthorization({
  orderReference: authReference,
});
```

## List Authorizations

```ts
const result = await mypos.listAuthorizations();
```

## Reading Order

1. `references/store-card.md` — obtain the card token first.
2. This file — authorize then capture or reverse.

## See Also

- references/store-card.md
- references/preauthorization.md
