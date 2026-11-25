> Docs: https://developers.mypos.com/sdks/smart-sdk/payment-request

## Send a Payment Request via SMS

```java
private static final int PAYMENT_REQUEST_CODE = 1007;

MyPOSPaymentRequest request = MyPOSPaymentRequest.builder()
    .productAmount(50.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .recipientPhone("+359888123456")
    .description("Invoice #1001")
    .build();

MyPOSAPI.openPaymentRequestActivity(this, request, PAYMENT_REQUEST_CODE);
```

## Send a Payment Request via QR Code

```java
MyPOSPaymentRequest request = MyPOSPaymentRequest.builder()
    .productAmount(50.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .requestType(MyPOSPaymentRequest.REQUEST_TYPE_QR)
    .description("Invoice #1001")
    .build();

MyPOSAPI.openPaymentRequestActivity(this, request, PAYMENT_REQUEST_CODE);
```

## Reading Order

1. `references/card-payment.md` — understand the basic result handling pattern.
2. This file — payment requests.

## See Also

- references/card-payment.md
