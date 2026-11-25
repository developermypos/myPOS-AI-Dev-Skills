> Docs: https://developers.mypos.com/apis/checkout-api/authorization
> Capture: https://developers.mypos.com/apis/checkout-api/authorization-capture
> Reverse: https://developers.mypos.com/apis/checkout-api/authorization-reverse
> List: https://developers.mypos.com/apis/checkout-api/authorization-list

The authorization flow reserves funds on a stored CardToken now and lets you capture (settle) or void the hold later. Use this for deferred billing — hotel check-in, subscriptions, marketplace payouts.

**Requires a CardToken** from a prior IPCPurchase or IPCIAStoreCard.

## Step 1 — Authorize (reserve funds)

```python
params = {
    "IPCmethod":    "IPCAuthorization",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "100.00",
    "Currency":     "EUR",
    "OrderID":      f"auth-{uuid.uuid4()}",
    "ItemName":     "Hotel Stay",
    "CardToken":    "BASE64_CARD_TOKEN",
}
params["Signature"] = compute_signature(params, private_key_pem)

response = requests.post("https://www.mypos.com/vmp/checkout-api", data=params, ...)
# On success: Status=0, IPCTrnref returned — store it for capture/reversal
```

## Step 2a — Capture (settle the authorization)

```python
params = {
    "IPCmethod":    "IPCAuthorizationCapture",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "95.00",   # can be ≤ authorized amount
    "Currency":     "EUR",
    "OrderID":      "ORIGINAL_ORDER_ID",  # same OrderID from authorization
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Step 2b — Reverse (void the authorization)

Releases the hold without charging the card:

```python
params = {
    "IPCmethod":    "IPCAuthorizationReverse",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "OrderID":      "ORIGINAL_ORDER_ID",
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Query Open Authorizations

```python
params = {
    "IPCmethod":    "IPCAuthorizationList",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: array of {Date, IPCTrnref, OrderID, Amount, Currency} for all un-captured authorizations
```

## Reading Order

1. `references/authentication.md` — signature computation
2. `references/store-card.md` — obtaining a CardToken (required before authorization)
3. This file — authorization flow

## See Also

- references/store-card.md
- references/preauthorization.md
- references/refund-reversal.md
