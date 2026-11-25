> Docs: https://developers.mypos.com/sdks/glass-sdk/api-reference/payments

## Print Merchant Receipt

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .printMerchantReceipt(MyPOSUtil.RECEIPT_ON)
    .build();
```

Printing requires an optional Bluetooth printer paired with the Glass device.

## E-Receipt via Email

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .eReceiptEmail("customer@example.com")
    .build();
```

## E-Receipt via SMS

```java
MyPOSPayment payment = MyPOSPayment.builder()
    // ...
    .eReceiptPhone("+359888123456")
    .build();
```

## Reading Order

1. `references/card-payment.md` — basic payment builder.
2. This file — add receipt options to the payment.

## See Also

- references/card-payment.md
