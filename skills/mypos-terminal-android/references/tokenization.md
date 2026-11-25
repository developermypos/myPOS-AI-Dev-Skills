> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Store a Card Token

During a payment, request card tokenization:

```java
posHandler.openPaymentActivity(
    activity,
    25.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    true,           // storeCard = true → returns a card token in the result
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                String cardToken = data.getCardToken();
                // Persist cardToken against the customer record
            }
        }
    }
);
```

## Charge a Stored Card

```java
posHandler.openStoredCardPaymentActivity(
    activity,
    storedCardToken,
    29.99,
    Currency.EUR,
    UUID.randomUUID().toString(),
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                // Recurring payment successful
            }
        }
    }
);
```

## Reading Order

1. `references/payment.md` — basic payment.
2. This file — add tokenization for recurring charges.

## See Also

- references/payment.md
