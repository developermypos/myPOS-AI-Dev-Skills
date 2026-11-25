> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Activate a Gift Card

```java
posHandler.openGiftCardActivity(
    activity,
    GiftCardOperation.ACTIVATE,
    50.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    new OnTransactionCompleteListener() {
        @Override
        public void onTransactionComplete(TransactionData data) {
            if (data.isApproved()) {
                // Gift card activated
            }
        }
    }
);
```

## Check Gift Card Balance

```java
posHandler.openGiftCardActivity(
    activity,
    GiftCardOperation.BALANCE_CHECK,
    0.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    listener
);
```

## Deactivate a Gift Card

```java
posHandler.openGiftCardActivity(
    activity,
    GiftCardOperation.DEACTIVATE,
    0.00,
    Currency.EUR,
    UUID.randomUUID().toString(),
    listener
);
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — gift card operations.

## See Also

- references/payment.md
