> Docs: https://developers.mypos.com/sdks/glass-sdk/api-reference/payment-request

## Send a Payment Request via QR Code

```java
private static final int PAYMENT_REQUEST_CODE = 2004;

MyPOSPaymentRequest request = MyPOSPaymentRequest.builder()
    .productAmount(50.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .requestType(MyPOSPaymentRequest.REQUEST_TYPE_QR)
    .description("Invoice #1001")
    .build();

MyPOSAPI.openPaymentRequestActivity(this, request, PAYMENT_REQUEST_CODE);
```

## Send via SMS

```java
MyPOSPaymentRequest request = MyPOSPaymentRequest.builder()
    .productAmount(50.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .requestType(MyPOSPaymentRequest.REQUEST_TYPE_SMS)
    .recipientPhone("+359888123456")
    .description("Invoice #1001")
    .build();
```

## Send via Email

```java
MyPOSPaymentRequest request = MyPOSPaymentRequest.builder()
    // ...
    .requestType(MyPOSPaymentRequest.REQUEST_TYPE_EMAIL)
    .recipientEmail("customer@example.com")
    .build();
```

## Reading Order

1. `references/card-payment.md` — understand the basic result handling.
2. This file — payment requests.

## See Also

- references/card-payment.md
