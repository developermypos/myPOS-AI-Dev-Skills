> Docs: https://developers.mypos.com/sdks/embedded-sdk/sample-code

## Standard Embedded Payment

The SDK renders the card form and a Pay button inside your container div.

```javascript
import * as MyPOSEmbedded from 'mypos-embedded-checkout';

const paymentParams = {
    sid: 'YOUR_STORE_ID',           // from myPOS merchant portal
    ipcLanguage: 'en',
    walletNumber: 'YOUR_WALLET_NUMBER',
    amount: 23.45,
    currency: 'EUR',
    orderID: crypto.randomUUID(),    // must be unique per transaction
    urlNotify: 'https://yoursite.com/payment-notify',  // HTTPS required
    urlOk: window.location.href,
    urlCancel: window.location.href,
    keyIndex: 1,
    cartItems: [
        {
            article: 'Product Name',
            quantity: 1,
            price: 23.45,
            currency: 'EUR',
        }
    ]
};

const callbackParams = {
    isSandbox: true,  // set false for production
    onSuccess: function (data) {
        // Do NOT fulfill the order here — verify server-side first
        fetch('/api/verify-payment', { method: 'POST', body: JSON.stringify(data) });
    },
    onError: function () {
        // Payment failed or was cancelled
    }
};

MyPOSEmbedded.createPayment(
    'myPOSEmbeddedCheckout',   // container div ID
    paymentParams,
    callbackParams
);
```

## paymentParams Fields

| Field | Required | Description |
|-------|----------|-------------|
| `sid` | Yes | Store ID from merchant portal |
| `walletNumber` | Yes | myPOS wallet/account number |
| `amount` | Yes | Decimal payment amount |
| `currency` | Yes | ISO 4217 code (e.g. `EUR`) |
| `orderID` | Yes | Unique reference per transaction |
| `urlNotify` | Yes | HTTPS IPN endpoint on your server |
| `urlOk` | Yes | Redirect URL after success |
| `urlCancel` | Yes | Redirect URL after cancel |
| `keyIndex` | Yes | Key index (usually `1`) |
| `ipcLanguage` | No | Form language code (default `en`) |
| `cartItems` | No | Array of line items |

## Reading Order

1. `references/setup.md` — install SDK and add container div first.
2. This file — standard card payment.
3. `references/webhooks.md` — implement server-side verification.

## See Also

- references/deferred-payment.md
- references/webhooks.md
