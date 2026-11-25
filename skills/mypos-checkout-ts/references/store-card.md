> Docs: https://developers.mypos.com/apis/checkout-api/in-app-store-card
> SDK sample: https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/stored-cards

## Store a Card (Tokenization)

Server-side: generate signed fields for the store-card flow.

```ts
const fields = mypos.generateStoreCardFields(
  `customer-${customerId}`,  // unique customer reference
  {
    urlOk: 'https://example.com/card/saved',
    urlCancel: 'https://example.com/card/cancel',
    urlNotify: 'https://example.com/api/mypos/notify',
  }
);
```

Client-side: render the form the same way as a purchase.

```tsx
import { PaymentForm } from 'mypos-online-checkout/react';

<PaymentForm fields={fields} checkoutUrl={mypos.checkoutUrl} />
```

## IPN Callback

After the customer completes the card entry, myPOS sends a notification to `urlNotify` containing the `cardToken`. Store this token against the customer record — use it with `references/charge-stored-card.md`.

## Reading Order

1. `references/setup.md` — initialize first.
2. This file — save the card.
3. `references/charge-stored-card.md` — charge the saved card later.
4. `references/webhooks.md` — receive the card token via IPN.

## See Also

- references/charge-stored-card.md
- references/webhooks.md
