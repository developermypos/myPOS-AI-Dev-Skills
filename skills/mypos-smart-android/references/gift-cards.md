> Docs: https://developers.mypos.com/sdks/smart-sdk/pl-giftcard

## Activate a Gift Card

```java
private static final int GIFTCARD_REQUEST_CODE = 1008;

MyPOSGiftCard activate = MyPOSGiftCard.builder()
    .operation(MyPOSGiftCard.ACTIVATE)
    .productAmount(50.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .build();

MyPOSAPI.openGiftCardActivity(this, activate, GIFTCARD_REQUEST_CODE);
```

## Check Gift Card Balance

```java
MyPOSGiftCard balance = MyPOSGiftCard.builder()
    .operation(MyPOSGiftCard.BALANCE_CHECK)
    .foreignTransactionId(UUID.randomUUID().toString())
    .build();

MyPOSAPI.openGiftCardActivity(this, balance, GIFTCARD_REQUEST_CODE);
```

## Deactivate a Gift Card

```java
MyPOSGiftCard deactivate = MyPOSGiftCard.builder()
    .operation(MyPOSGiftCard.DEACTIVATE)
    .foreignTransactionId(UUID.randomUUID().toString())
    .build();

MyPOSAPI.openGiftCardActivity(this, deactivate, GIFTCARD_REQUEST_CODE);
```

## Reading Order

1. `references/setup.md` — SDK setup.
2. This file — gift card operations.

## See Also

- references/card-payment.md
