> Docs (store during checkout): https://developers.mypos.com/apis/checkout-api/purchase
> Docs (in-app store): https://developers.mypos.com/apis/checkout-api/in-app-store-card
> Docs (update): https://developers.mypos.com/apis/checkout-api/in-app-store-card-update

Cards are stored as opaque Base64 tokens (`CardToken`). Once stored, use the token for IPCIAPurchase, IPCAuthorization, or IPCIAPreAuthorization — no raw card data needed.

## Option A — Store During Redirect Checkout

Add `CardTokenRequest` to an IPCPurchase request:

```python
params = {
    # ... all normal IPCPurchase params ...
    "CardTokenRequest": "1",   # 1 = store new card, 2 = pay + store
}
# After successful payment, IPCPurchaseNotify includes a CardToken field
```

The token arrives in the `IPCPurchaseNotify` webhook body. Store it associated with the customer.

## Option B — Store Directly (IPCIAStoreCard)

No payment — just stores the card. Requires PCI SAQ-D if sending raw card data:

```python
params = {
    "IPCmethod":        "IPCIAStoreCard",
    "IPCVersion":       "1.4",
    "IPCLanguage":      "EN",
    "SID":              "YOUR_SID",
    "WalletNumber":     "YOUR_WALLET",
    "KeyIndex":         "1",
    "PAN":              "4111111111111111",
    "CardholderName":   "JANE DOE",
    "ExpDate":          "1226",    # MMYY
    "CVC":              "123",
    "ECI":              "7",
    "CardVerification": "1",       # 1 = no charge, 2 = charge then refund to verify
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: CardToken (Base64), PAN (last 4 digits), ExpDate, CardType
```

## Option C — Update a Stored Card (IPCIAStoredCardUpdate)

Update the expiry, cardholder name, or CVC on an existing token:

```python
params = {
    "IPCmethod":        "IPCIAStoredCardUpdate",
    "IPCVersion":       "1.4",
    "IPCLanguage":      "EN",
    "SID":              "YOUR_SID",
    "WalletNumber":     "YOUR_WALLET",
    "KeyIndex":         "1",
    "CardToken":        "EXISTING_BASE64_TOKEN",
    "CardholderName":   "JANE DOE",
    "CardType":         "VISA",
    "ExpDate":          "0128",    # MMYY — updated expiry
    "CVC":              "456",
    "ECI":              "7",
    "CardVerification": "1",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: Updated CardToken, PAN last 4, ExpDate, CardType
```

## Reading Order

1. `references/authentication.md` — signature computation
2. This file — card storage options
3. `references/server-side-payment.md` — using the CardToken to charge
4. `references/authorization.md` — using the CardToken to authorize

## See Also

- references/server-side-payment.md
- references/authorization.md
- references/webhooks.md
