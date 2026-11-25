> Docs (refund): https://developers.mypos.com/apis/checkout-api/refund
> Docs (reversal): https://developers.mypos.com/apis/checkout-api/reversal

**Refund** (IPCRefund): Return funds to the customer after settlement. Can be partial or full. Requires the `IPC_Trnref` from the original transaction (returned in `IPCPurchaseNotify`).

**Reversal** (IPCReversal): Cancel a transaction on the same day before settlement. No `IPC_Trnref` needed — identified by `OrderID`.

## Refund

```python
params = {
    "IPCmethod":    "IPCRefund",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "IPC_Trnref":   "TRNREF_FROM_PURCHASE_NOTIFY",  # required — from original transaction
    "Amount":       "10.00",     # refund amount; ≤ original payment amount
    "Currency":     "EUR",
}
params["Signature"] = compute_signature(params, private_key_pem)

response = requests.post("https://www.mypos.com/vmp/checkout-api", data=params, ...)
# Response: Status=0 on success, new IPC_Trnref for the refund transaction
```

For a full refund, pass the original `Amount` exactly.

## Reversal

```python
params = {
    "IPCmethod":    "IPCReversal",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "OrderID":      "ORIGINAL_ORDER_ID",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: Status=0 on success
```

Reversal is only available on the same business day as the original transaction. Use IPCRefund for post-settlement returns.

## Reading Order

1. `references/authentication.md` — signature computation
2. `references/webhooks.md` — IPC_Trnref comes from IPCPurchaseNotify
3. This file — refund/reversal

## See Also

- references/webhooks.md
- references/purchase.md
