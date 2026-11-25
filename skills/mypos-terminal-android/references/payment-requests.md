> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Send a Payment Request via Email

```java
posHandler.openPaymentRequestActivity(
    activity,
    50.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    "customer@example.com",
    PaymentRequestType.EMAIL,
    "Invoice #2001",
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                // Request sent / payment received
            }
        }
    }
);
```

## Send via SMS

```java
posHandler.openPaymentRequestActivity(
    activity,
    50.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    "+359888123456",
    PaymentRequestType.SMS,
    "Invoice #2001",
    listener
);
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — payment requests.

## See Also

- references/payment.md
