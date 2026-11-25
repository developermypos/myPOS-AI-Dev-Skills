> Docs (send money): https://developers.mypos.com/apis/checkout-api/send-money
> Docs (request money): https://developers.mypos.com/apis/checkout-api/request-money
> Docs (mandate management): https://developers.mypos.com/apis/checkout-api/mandate-management

These three operations transfer funds between myPOS accounts (not card payments). They're used for split payments, payouts, and recurring direct debits within the myPOS ecosystem.

## Send Money (IPCSendMoney)

Push funds from your wallet to another myPOS account:

```python
params = {
    "IPCmethod":            "IPCSendMoney",
    "IPCVersion":           "1.4",
    "IPCLanguage":          "EN",
    "SID":                  "YOUR_SID",
    "WalletNumber":         "YOUR_WALLET",
    "KeyIndex":             "1",
    "CustomerWalletNumber": "RECIPIENT_WALLET_NUMBER",
    "Amount":               "50.00",
    "Currency":             "EUR",
    "TransactionReference": f"transfer-{uuid.uuid4()}",  # your unique reference
    "Reason":               "Invoice #1234 payment",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: Status, IPC_Trnref, Amount, Currency, CustomerWalletNumber
```

## Request Money (IPCRequestMoney)

Pull funds from another myPOS account using a pre-registered mandate:

```python
params = {
    "IPCmethod":            "IPCRequestMoney",
    "IPCVersion":           "1.4",
    "IPCLanguage":          "EN",
    "SID":                  "YOUR_SID",
    "WalletNumber":         "YOUR_WALLET",
    "KeyIndex":             "1",
    "MandateReference":     "MANDATE_REF_FROM_REGISTRATION",
    "CustomerWalletNumber": "PAYER_WALLET_NUMBER",
    "OrderID":              f"pull-{uuid.uuid4()}",
    "Amount":               "99.00",
    "Currency":             "EUR",
    "Reason":               "Monthly subscription",
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: Status, MandateReference, CustomerWalletNumber, IPC_Trnref, ReversalIndicator
```

A mandate must be registered before calling IPCRequestMoney — see below.

## Mandate Management (IPCMandateManagement)

Register or cancel a mandate that authorizes recurring pulls:

```python
# Register a new mandate
params = {
    "IPCmethod":            "IPCMandateManagement",
    "IPCVersion":           "1.4",
    "IPCLanguage":          "EN",
    "SID":                  "YOUR_SID",
    "WalletNumber":         "YOUR_WALLET",
    "KeyIndex":             "1",
    "MandateReference":     f"mandate-{uuid.uuid4()}",  # your unique reference — store it
    "CustomerWalletNumber": "PAYER_WALLET_NUMBER",
    "Action":               "1",   # 1 = register, 2 = cancel
}
params["Signature"] = compute_signature(params, private_key_pem)
# Response: Status, MandateReference, CustomerWalletNumber, Action

# Cancel a mandate
params["Action"] = "2"
params["MandateReference"] = "EXISTING_MANDATE_REF"
params["Signature"] = compute_signature(params, private_key_pem)
```

## Reading Order

1. `references/authentication.md` — signature computation
2. This file — wallet-to-wallet transfers and mandates

## See Also

- references/webhooks.md
- references/refund-reversal.md
