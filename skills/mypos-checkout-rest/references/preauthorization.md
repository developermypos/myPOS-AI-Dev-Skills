> Docs: https://developers.mypos.com/apis/checkout-api/pre-authorization
> In-app pre-auth: https://developers.mypos.com/apis/checkout-api/in-app-pre-authorization
> Completion: https://developers.mypos.com/apis/checkout-api/pre-auth-completion
> Cancellation: https://developers.mypos.com/apis/checkout-api/pre-auth-cancellation
> Status: https://developers.mypos.com/apis/checkout-api/pre-auth-status

Pre-authorization holds funds on the customer's card. You complete (capture) or cancel the hold later once the final amount is known. Two variants: redirect (customer goes to myPOS page) and in-app (server-to-server, requires CardToken or raw card).

## Redirect Pre-Authorization (IPCPreAuthorization)

Works like IPCPurchase — submit as an HTML form. Use `https://www.mypos.com/vmp/checkout-test` for sandbox, `https://www.mypos.com/vmp/checkout` for production:

```python
params = {
    "IPCmethod":    "IPCPreAuthorization",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "150.00",
    "Currency":     "EUR",
    "OrderID":      f"preauth-{uuid.uuid4()}",
    "URL_OK":       "https://example.com/preauth/success",
    "URL_Cancel":   "https://example.com/preauth/cancel",
    "URL_Notify":   "https://example.com/api/preauth/notify",
    "CartItems":    "1",
    "Article_1":    "Car Rental Hold",
    "Quantity_1":   "1",
    "Price_1":      "150.00",
    "Amount_1":     "150.00",
    "Currency_1":   "EUR",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Submit as HTML form (same as IPCPurchase)
```

Webhook: `IPCPreAuthorizationNotify` arrives at `URL_Notify` — verify its signature before trusting.

## In-App Pre-Authorization (IPCIAPreAuthorization)

Server-to-server, using CardToken or raw card data:

```python
params = {
    "IPCmethod":    "IPCIAPreAuthorization",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "150.00",
    "Currency":     "EUR",
    "OrderID":      f"preauth-{uuid.uuid4()}",
    "ItemName":     "Car Rental Hold",
    "CardToken":    "BASE64_CARD_TOKEN",   # or use PAN/CVC/ExpDate (PCI SAQ-D)
}
params["Signature"] = compute_signature(params, private_key_pem)
# POST to https://www.mypos.com/vmp/checkout-api
```

## Completion (Capture)

Call after the final amount is known:

```python
params = {
    "IPCmethod":    "IPCPreAuthCompletion",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "135.00",              # final charge ≤ original pre-auth
    "Currency":     "EUR",
    "OrderID":      "ORIGINAL_ORDER_ID",
    "IPC_Trnref":   "REFERENCE_FROM_NOTIFY",
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Cancellation (Release Hold)

```python
params = {
    "IPCmethod":    "IPCPreAuthCancellation",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "OrderID":      "ORIGINAL_ORDER_ID",
    "IPC_Trnref":   "REFERENCE_FROM_NOTIFY",
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Status Check

```python
params = {
    "IPCmethod":    "IPCPreAuthStatus",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "OrderID":      "ORIGINAL_ORDER_ID",
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Reading Order

1. `references/authentication.md` — signature computation
2. This file — pre-authorization flow
3. `references/webhooks.md` — IPCPreAuthorizationNotify handling

## See Also

- references/authorization.md
- references/refund-reversal.md
