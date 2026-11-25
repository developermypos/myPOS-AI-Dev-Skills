> Docs: https://developers.mypos.com/apis/checkout-api/payment-session-create

IPCPaymentSessionCreate generates a `SessionToken` that is passed to the myPOS Embedded Checkout SDK (`mypos-embedded-checkout` npm package) to initialize the embedded payment form. If you are using the Embedded Checkout SDK, call this server-side first to get the token, then pass it to the client.

See also: the `mypos-checkout-embedded` skill for the full embedded checkout integration.

## Create a Payment Session

POST to `https://www.mypos.com/vmp/checkout-api` as `application/x-www-form-urlencoded`:

```python
params = {
    "IPCmethod":    "IPCPaymentSessionCreate",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "45.99",
    "Currency":     "EUR",
    "OrderID":      f"order-{uuid.uuid4()}",   # must be unique
    "URL_Notify":   "https://example.com/api/payment/notify",
    "CartItems":    "1",
    "Article_1":    "Product Name",
    "Quantity_1":   "1",
    "Price_1":      "45.99",
    "Amount_1":     "45.99",
    "Currency_1":   "EUR",
}
params["Signature"] = compute_signature(params, private_key_pem)

response = requests.post("https://www.mypos.com/vmp/checkout-api", data=params, ...)
```

## Response

```xml
<Result>
  <Status>0</Status>
  <StatusMsg>Success</StatusMsg>
  <SessionToken>BASE64_SESSION_TOKEN</SessionToken>
  <Created>2024-01-15 10:30:00</Created>
  <ExpDate>2024-01-15 11:30:00</ExpDate>
  <Signature>...</Signature>
</Result>
```

Verify the response `Signature` before using the `SessionToken`. The token expires at `ExpDate` — typically 1 hour.

## Passing the Token to the Frontend

```javascript
// Node.js server route
app.post('/api/create-payment-session', async (req, res) => {
    const sessionToken = await createPaymentSession(req.body.amount, req.body.orderId);
    res.json({ sessionToken });
});

// Client — Embedded Checkout SDK
const sessionToken = await fetch('/api/create-payment-session', {...}).then(r => r.json()).then(d => d.sessionToken);
MyPOSEmbedded.createApplePayButton('container', sessionToken, { isSandbox: true, ... });
```

## Reading Order

1. `references/authentication.md` — signature computation
2. This file — create a payment session
3. See `mypos-checkout-embedded` skill for the full embedded form integration

## See Also

- references/purchase.md
- references/webhooks.md
