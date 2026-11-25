> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Refund a Payment

```java
posHandler.openRefundActivity(
    activity,
    originalTransactionReference,
    refundAmount,
    Currency.EUR,
    UUID.randomUUID().toString(),
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                // Refund successful
            }
        }
    }
);
```

## Reverse a Payment (Same-Day Cancellation)

```java
posHandler.openReversalActivity(
    activity,
    originalTransactionReference,
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                // Reversal successful
            }
        }
    }
);
```

## Reading Order

1. `references/payment.md` — the original payment.
2. This file — refund or reverse it.

## See Also

- references/payment.md
