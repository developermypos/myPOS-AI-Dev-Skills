> Docs: https://developers.mypos.com/sdks/online-checkout-typescript-sdk/sample-code/purchase
> API reference: https://developers.mypos.com/apis/checkout-api/purchase

## Server: Generate Signed Purchase Fields

```ts
// Server action / API route
import { mypos } from '@/lib/mypos'; // your initialized MyPOSClient singleton

const fields = mypos.generatePurchaseFields(
  {
    cartItems: [{ name: 'Product', quantity: 1, unit: 'pcs', unitPrice: 25.00 }],
    totalAmount: 25.00,
    currency: 'EUR',
  },
  `order-${Date.now()}`,  // unique order reference
  {
    urlOk: 'https://example.com/payment/success',
    urlCancel: 'https://example.com/payment/cancel',
    urlNotify: 'https://example.com/api/mypos/notify',
  }
);
```

## Client: Render the Payment Form

```tsx
import { PaymentForm } from 'mypos-online-checkout/react';

export default function CheckoutPage({ fields, checkoutUrl }) {
  return (
    <PaymentForm
      fields={fields}
      checkoutUrl={checkoutUrl}
    />
  );
}
```

The browser submits this form to the myPOS hosted checkout. The customer enters card details on myPOS's page — your server never sees raw card data.

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — purchase flow.
3. `references/webhooks.md` — handle the IPN notification after payment.

## See Also

- references/setup.md
- references/webhooks.md
- references/refund-reversal.md
