> Docs: https://developers.mypos.com/online-payments/initial-setup-and-testing/ipn

## Why Server-Side Verification is Required

The client-side `onSuccess` callback fires when the iFrame reports success, but it is not cryptographically verified. Always confirm payment with a server-side `IPCGetPaymentStatus` call before fulfilling any order.

## urlNotify Requirements

- Must use **HTTPS** (plain HTTP is rejected)
- Your server must return **HTTP 200** with response body exactly `"OK"`
- Any other response code or body causes myPOS to treat the notification as undelivered and retry

## Minimal IPN Handler (Node.js / Express)

```javascript
app.post('/payment-notify', async (req, res) => {
    const { orderID } = req.body;

    // Always verify with the API — never trust client data alone
    const status = await getPaymentStatus(orderID);

    if (status === 'SUCCESS') {
        await fulfillOrder(orderID);
    }

    // Must respond "OK" — not JSON, not 204, not anything else
    res.status(200).send('OK');
});
```

## Verify Payment Status (IPCGetPaymentStatus)

```javascript
async function getPaymentStatus(orderID) {
    const response = await fetch('https://www.mypos.com/vmp/checkout-test/IPCGetPaymentStatus', {
        method: 'POST',
        body: new URLSearchParams({
            SID: process.env.MYPOS_SID,
            WalletNumber: process.env.MYPOS_WALLET,
            OrderID: orderID,
            KeyIndex: '1',
            // ... signature fields
        })
    });
    const data = await response.json();
    return data.Status === 0 ? 'SUCCESS' : 'FAILED';
}
```

## Reading Order

1. `references/card-payment.md` — set `urlNotify` in `paymentParams`.
2. This file — implement the IPN handler.

## See Also

- references/card-payment.md
- https://developers.mypos.com/online-payments/initial-setup-and-testing/ipn
