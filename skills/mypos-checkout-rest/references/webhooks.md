> Docs (notify): https://developers.mypos.com/apis/checkout-api/purchase-notify
> Docs (status): https://developers.mypos.com/apis/checkout-api/get-payment-status
> Docs (rollback): https://developers.mypos.com/apis/checkout-api/purchase-rollback

## IPCPurchaseNotify — Receiving Payment Notifications

myPOS POSTs notification data to your `URL_Notify` after a completed payment. This is the authoritative signal — do not rely on `URL_OK` redirect.

```python
from flask import request, Response

@app.route('/api/payment/notify', methods=['POST'])
def payment_notify():
    data = request.form.to_dict()
    
    # Step 1: verify signature BEFORE processing anything
    if not verify_response_signature(data, api_public_key_pem):
        return Response("Signature invalid", status=403)
    
    ipc_method = data.get("IPCmethod")  # "IPCPurchaseNotify"
    status = data.get("Status")
    order_id = data.get("OrderID")
    ipc_trnref = data.get("IPC_Trnref")   # store for refunds
    amount = data.get("Amount")
    card_token = data.get("CardToken")     # present if CardTokenRequest was set
    
    if status == "0":
        # Payment succeeded — fulfill the order
        fulfill_order(order_id, ipc_trnref, card_token)
    else:
        # Payment failed — log the StatusMsg
        log_payment_failure(order_id, data.get("StatusMsg"))
    
    # Must respond exactly HTTP 200 — any other response triggers retry
    return Response("OK", status=200)
```

### Key Fields in IPCPurchaseNotify

| Field | Description |
|-------|-------------|
| `Status` | `0` = success |
| `OrderID` | Your original OrderID |
| `IPC_Trnref` | myPOS transaction reference — store for refunds |
| `Amount` / `Currency` | Charged amount |
| `CardToken` | Returned if `CardTokenRequest` was set |
| `PAN` | Last 4 digits of card |
| `CardType` | VISA, MASTERCARD, etc. |
| `CustomerEmail` | If customer provided |
| `RequestDateTime` | Transaction timestamp |

## IPCGetPaymentStatus — Poll for Payment Status

Use when you need to check status without waiting for the webhook (e.g., timeout recovery):

```python
params = {
    "IPCmethod":    "IPCGetPaymentStatus",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "OrderID":      "YOUR_ORDER_ID",
}
params["Signature"] = compute_signature(params, private_key_pem)

response = requests.post("https://www.mypos.com/vmp/checkout-api", data=params, ...)
# Response: Status, StatusMsg, IPC_Trnref, Amount, Currency, Signature
```

## IPCPurchaseRollback — Transaction Reversed

If your `URL_Notify` endpoint fails to respond HTTP 200, myPOS may reverse the transaction and send `IPCPurchaseRollback`. Handle it the same way — verify signature, then reverse the fulfillment:

```python
if data.get("IPCmethod") == "IPCPurchaseRollback":
    order_id = data.get("OrderID")
    reverse_fulfillment(order_id)
    return Response("OK", status=200)
```

## Reading Order

1. `references/authentication.md` — signature verification algorithm
2. This file — webhook handling
3. `references/refund-reversal.md` — using IPC_Trnref to issue refunds

## See Also

- references/refund-reversal.md
- references/purchase.md
