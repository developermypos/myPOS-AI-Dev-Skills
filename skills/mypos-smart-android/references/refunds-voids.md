> Refund docs: https://developers.mypos.com/sdks/smart-sdk/refund
> Void docs: https://developers.mypos.com/sdks/smart-sdk/void

## Refund a Transaction

```java
private static final int REFUND_REQUEST_CODE = 1002;

MyPOSRefund refund = MyPOSRefund.builder()
    .refundAmount(13.37)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(originalRef)  // from the original payment result
    .build();

MyPOSAPI.openRefundActivity(this, refund, REFUND_REQUEST_CODE);
```

## Void (Cancel) a Transaction

```java
private static final int VOID_REQUEST_CODE = 1003;

MyPOSVoid voidRequest = MyPOSVoid.builder()
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(originalRef)
    .build();

MyPOSAPI.openVoidActivity(this, voidRequest, VOID_REQUEST_CODE);
```

Both refund and void results are received in `onActivityResult()` using the same pattern as payments.

## Reading Order

1. `references/card-payment.md` — the original payment being refunded/voided.
2. This file — refund or void.

## See Also

- references/card-payment.md
