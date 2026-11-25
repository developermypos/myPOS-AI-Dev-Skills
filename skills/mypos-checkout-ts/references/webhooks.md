> IPN docs: https://developers.mypos.com/apis/checkout-api/purchase-notify
> Callback reference: https://developers.mypos.com/apis/checkout-api/checkout-getting-started

## Receive and Validate an IPN Notification

myPOS sends a POST request to your `urlNotify` after every payment event. Always validate the signature before trusting the data.

```ts
// Next.js App Router example
import { mypos } from '@/lib/mypos';
import { NextRequest, NextResponse } from 'next/server';

export async function POST(req: NextRequest) {
  const formData = await req.formData();

  const isValid = mypos.validateWebhook(formData);
  if (!isValid) {
    return NextResponse.json({ error: 'Invalid signature' }, { status: 400 });
  }

  const status = formData.get('Status');
  const orderReference = formData.get('OrderReference') as string;

  if (status === 'OK') {
    await updateOrderStatus(orderReference, 'paid');
  }

  // Always respond with 200 immediately
  return new NextResponse(null, { status: 200 });
}
```

## IPN Fields

| Field | Description |
|-------|-------------|
| `Status` | `OK` (success), `CANCELED`, `FAILED` |
| `OrderReference` | Your unique order reference |
| `TransactionID` | myPOS transaction identifier |
| `Amount` | Amount charged |
| `Currency` | ISO currency code |
| `CardToken` | Returned for Store Card flow |

## Reading Order

1. `references/purchase.md` — set `urlNotify` in the payment request.
2. This file — implement the IPN handler.

## See Also

- references/purchase.md
- https://developers.mypos.com/apis/checkout-api/purchase-notify-ok
- https://developers.mypos.com/apis/checkout-api/purchase-notify
