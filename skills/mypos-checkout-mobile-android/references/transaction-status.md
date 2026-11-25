> Docs: https://developers.mypos.com/in-app-payments/android-payments/check-transaction-status

## Check Transaction Status

Use this to confirm a payment succeeded, verify a refund went through, resolve an unknown result
state, or reconcile transactions in the background — never rely on on-device UI state alone for
high-value order fulfillment.

```java
IPCGetTransactionStatus ipcGetTransactionStatus = new IPCGetTransactionStatus();
ipcGetTransactionStatus.setOrderId(orderId);
ipcGetTransactionStatus.setTxtType(MyPos.TRANSACTION_TYPE_PURCHASE); // or TRANSACTION_TYPE_REFUND
ipcGetTransactionStatus.setOnCommandCompleteListener(new IPCGetTransactionStatus.OnCommandCompleteListener() {
    @Override
    public void onCommandComplete(final int transactionStatus, final String transactionReference) { }

    @Override
    public void onError(final int status) { }
});
ipcGetTransactionStatus.sendRequest();
```

## Reading Order

1. `references/purchase.md` or `references/refund.md` — the transaction being checked.
2. This file — confirm its status.

## See Also

- references/purchase.md
- references/refund.md
- references/preauthorization.md
