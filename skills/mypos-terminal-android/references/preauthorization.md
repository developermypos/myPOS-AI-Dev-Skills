> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Pre-Authorize (Hold Funds)

```java
posHandler.openPreauthorizationActivity(
    activity,
    100.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                String preauthRef = data.getTransactionReference();
                // Store preauthRef for completion or cancellation
            }
        }
    }
);
```

## Complete Pre-Authorization (Capture)

```java
posHandler.openPreauthorizationCompletionActivity(
    activity,
    preauthRef,
    85.00,          // can be less than authorized amount
    Currency.EUR,
    UUID.randomUUID().toString(),
    listener
);
```

## Cancel Pre-Authorization

```java
posHandler.openPreauthorizationCancellationActivity(
    activity,
    preauthRef,
    UUID.randomUUID().toString(),
    listener
);
```

## Reading Order

1. `references/payment.md` — basic payment pattern.
2. This file — pre-authorization.

## See Also

- references/payment.md
