> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Initiate a Card Payment

After connecting to the terminal (`references/connection.md`):

```java
POSHandler posHandler = POSHandler.getInstance();

posHandler.openPaymentActivity(
    activity,
    25.00,                                // amount
    Currency.EUR,
    UUID.randomUUID().toString(),          // unique transaction reference
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                String ref = data.getTransactionReference();
                // Payment successful
            } else {
                String reason = data.getDeclineReason();
                // Payment declined or failed
            }
        }
    }
);
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — payment.
3. `references/refund-reversal.md` — handle refunds.

## See Also

- references/refund-reversal.md
- references/preauthorization.md
