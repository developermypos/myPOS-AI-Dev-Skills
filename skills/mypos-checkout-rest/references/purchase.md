> Docs: https://developers.mypos.com/apis/checkout-api/purchase

IPCPurchase redirects the customer to the myPOS hosted checkout page. Card data never touches your server.

## Request Parameters

Build a parameter map, compute the `Signature`, then POST it as an HTML form.

| Environment | Form action URL |
|-------------|----------------|
| **Sandbox** | `https://www.mypos.com/vmp/checkout-test` |
| **Production** | `https://www.mypos.com/vmp/checkout` |

```python
params = {
    "IPCmethod":      "IPCPurchase",
    "IPCVersion":     "1.4",
    "IPCLanguage":    "EN",
    "SID":            "YOUR_SID",
    "WalletNumber":   "YOUR_WALLET",
    "KeyIndex":       "1",
    "Amount":         "25.00",
    "Currency":       "EUR",
    "OrderID":        f"order-{uuid.uuid4()}",   # must be unique
    "URL_OK":         "https://example.com/payment/success",
    "URL_Cancel":     "https://example.com/payment/cancel",
    "URL_Notify":     "https://example.com/api/payment/notify",  # webhook
    "CartItems":      "1",
    "Article_1":      "Product Name",
    "Quantity_1":     "1",
    "Price_1":        "25.00",
    "Amount_1":       "25.00",
    "Currency_1":     "EUR",
    # Optional: store card for future use
    # "CardTokenRequest": "1",  # 1 = store new token, 2 = pay and store
}
params["Signature"] = compute_signature(params, private_key_pem)
```

Submit as an HTML auto-submit form (use the appropriate URL for the environment):

```html
<form method="POST" action="https://www.mypos.com/vmp/checkout-test" id="mypos-form">
  <!-- sandbox — change to /vmp/checkout for production -->
  <!-- render one hidden input per param -->
  {% for key, value in params.items() %}
  <input type="hidden" name="{{ key }}" value="{{ value }}">
  {% endfor %}
</form>
<script>document.getElementById("mypos-form").submit();</script>
```

## Optional Parameters

| Parameter | Description |
|-----------|-------------|
| `PaymentMethod` | `1`=Card only, `2`=iDEAL, `3`=All methods, `4`=Satispay, `5`=TWINT, `6`=IRIS |
| `CardTokenRequest` | `0`=no token (default), `1`=store new, `2`=pay + store |
| `CustomerEmail` | Pre-fill customer email |
| `CustomerFirstNames` / `CustomerFamilyName` | Pre-fill customer name |
| `PaymentParametersRequired` | `1`=full address, `2`=simplified, `3`=simplified + card-only form |
| `expires_in` | Session expiry in seconds (default 86400 / 24h) |
| `AccountSettlement` | Route payment to a specific settlement account |

## Redirect Callbacks

After payment, myPOS redirects the browser:
- **Success** → `URL_OK` with query params including `OrderID`, `IPC_Trnref`, `Status=0`
- **Cancel** → `URL_Cancel` with `OrderID`

**Do not fulfill orders based solely on URL_OK** — the redirect can be spoofed. Always wait for and verify the `IPCPurchaseNotify` webhook before marking an order as paid.

## Reading Order

1. `references/setup.md` — credentials and common parameters
2. `references/authentication.md` — how to compute Signature
3. This file — redirect checkout flow
4. `references/webhooks.md` — handle IPCPurchaseNotify after payment

## See Also

- references/webhooks.md
- references/store-card.md
- references/refund-reversal.md
