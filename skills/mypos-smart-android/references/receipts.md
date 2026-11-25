> Docs: https://developers.mypos.com/sdks/smart-sdk/payments

## Include a Custom Bitmap on the Receipt

```java
Bitmap logo = BitmapFactory.decodeResource(getResources(), R.drawable.company_logo);

MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .receiptBitmap(logo)         // printed at the top of the receipt
    .printMerchantReceipt(MyPOSUtil.RECEIPT_ON)
    .printCustomerReceipt(MyPOSUtil.RECEIPT_ON)
    .build();

MyPOSAPI.openPaymentActivity(this, payment, PAYMENT_REQUEST_CODE);
```

## Print a QR Code on the Receipt

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .receiptQRCodeData("https://example.com/order/12345")
    .build();
```

## E-Receipt (Email or SMS)

```java
MyPOSPayment payment = MyPOSPayment.builder()
    // ...
    .eReceiptEmail("customer@example.com")  // or .eReceiptPhone("+359...")
    .build();
```

## Reading Order

1. `references/card-payment.md` — basic payment builder.
2. This file — receipt customization.

## See Also

- references/advanced.md
