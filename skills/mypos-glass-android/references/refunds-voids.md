> Refund: https://developers.mypos.com/sdks/glass-sdk/api-reference/refund
> Void: https://developers.mypos.com/sdks/glass-sdk/api-reference/void

## Refund a Transaction

```java
private static final int REFUND_REQUEST_CODE = 2002;

MyPOSRefund refund = MyPOSRefund.builder()
    .refundAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(originalRef)
    .build();

MyPOSAPI.openRefundActivity(this, refund, REFUND_REQUEST_CODE);
```

## Void a Transaction

```java
private static final int VOID_REQUEST_CODE = 2003;

MyPOSVoid voidRequest = MyPOSVoid.builder()
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(originalRef)
    .build();

MyPOSAPI.openVoidActivity(this, voidRequest, VOID_REQUEST_CODE);
```

Both results are received in `onActivityResult()` using the same pattern as card-payment.

## Reading Order

1. `references/card-payment.md` — the original payment being refunded/voided.
2. This file — refund or void.

## See Also

- references/card-payment.md
