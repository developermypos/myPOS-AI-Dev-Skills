> Docs: https://developers.mypos.com/sdks/embedded-sdk/apple-and-google-pay

## Prerequisites

- Active myPOS merchant account with SID and Wallet Number
- HTTPS domain (required for both Apple Pay and Google Pay)
- Server-side ability to call the myPOS `PaymentSessionCreate` API

## Step 1 — Server-Side: Create a Payment Session

Your server calls the `PaymentSessionCreate` API and returns the `sessionToken` to the client.

```javascript
// Server-side (Node.js example)
const response = await fetch('https://api.mypos.com/payment-session', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({
        SID: process.env.MYPOS_SID,
        WalletNumber: process.env.MYPOS_WALLET,
        Amount: 23.45,
        Currency: 'EUR',
        OrderID: crypto.randomUUID(),
        CartItems: 1
    })
});
const { SessionToken } = await response.json();
// Pass SessionToken to the client
```

## Step 2 — Client-Side: Render the Button

```html
<div id="embeddedButtons"></div>
```

```javascript
if (MyPOSEmbedded.IS_APPLE_PAY_AVAILABLE) {
    MyPOSEmbedded.createApplePayButton(
        'embeddedButtons',
        sessionToken,
        {
            isSandbox: true,
            merchantName: 'My Store',   // displayed in Apple Pay sheet
            onSuccess: function (data) {
                // Verify server-side via IPCGetPaymentStatus
            },
            onDecline: function (err) {
                console.error(err);
            }
        }
    );
} else {
    MyPOSEmbedded.createGooglePayButton(
        'embeddedButtons',
        sessionToken,
        {
            isSandbox: true,
            onSuccess: function (data) {
                // Verify server-side via IPCGetPaymentStatus
            },
            onDecline: function (err) {
                console.error(err);
            }
        }
    );
}
```

## Step 3 — Apple Pay Domain Verification (Apple Pay Only)

Apple Pay requires two separate one-time setup steps:

**3a. Host the domain association file:**

```
https://yoursite.com/.well-known/apple-developer-merchantid-domain-association
```

Download the file from your Apple Developer account and host it at this exact path with content-type `text/plain`.

**3b. Register your domain in myPOS account:**

1. Log in to your myPOS merchant account
2. Go to **Store Settings**
3. Enter your site URL in the **"Embedded Apple Pay button URL"** field
4. Add the URL to the **"Request URLs"** list
5. Save changes

Both steps must be complete before `IS_APPLE_PAY_AVAILABLE` returns `true` for your domain.

## Step 4 — Verify Payment Server-Side

After `onSuccess`, call `IPCGetPaymentStatus` on your server with the order ID before fulfilling the order.

## Reading Order

1. `references/setup.md` — SDK setup.
2. `references/card-payment.md` — understand the payment flow.
3. This file — Apple/Google Pay.

## See Also

- references/webhooks.md
- https://developers.mypos.com/sdks/embedded-sdk/apple-and-google-pay
