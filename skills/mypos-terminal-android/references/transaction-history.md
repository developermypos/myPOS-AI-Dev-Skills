> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Get Transaction History

```java
posHandler.getTransactionHistory(
    activity,
    new OnTransactionHistoryListener() {
        @Override
        public void onReceive(List<TransactionData> transactions) {
            for (TransactionData tx : transactions) {
                String ref = tx.getTransactionReference();
                double amount = tx.getAmount();
                String status = tx.getStatus();
            }
        }
    }
);
```

## Reprint Last Receipt

```java
posHandler.reprintLastReceipt(activity, new OnReceiptPrintListener() {
    @Override
    public void onComplete(boolean success) {
        // Done
    }
});
```

## Get Last Transaction

```java
posHandler.getLastTransaction(new OnTransactionCompleteListener() {
    @Override
    public void onTransactionComplete(TransactionData data) {
        String ref = data.getTransactionReference();
        boolean approved = data.isApproved();
    }
});
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — transaction history and receipt operations.

## See Also

- references/receipts.md
