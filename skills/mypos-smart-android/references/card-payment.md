> Docs: https://developers.mypos.com/sdks/smart-sdk/payments
> Responses: https://developers.mypos.com/sdks/smart-sdk/responses

## Initiate a Card Payment

```java
private static final int PAYMENT_REQUEST_CODE = 1001;

MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(13.37)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())  // unique per transaction
    .printMerchantReceipt(MyPOSUtil.RECEIPT_ON)
    .printCustomerReceipt(MyPOSUtil.RECEIPT_ON)
    .build();

MyPOSAPI.openPaymentActivity(this, payment, PAYMENT_REQUEST_CODE);
```

## Handle the Result

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);

    if (requestCode == PAYMENT_REQUEST_CODE && resultCode == RESULT_OK && data != null) {
        int status = data.getIntExtra("status", TransactionProcessingResult.TRANSACTION_FAILED);
        boolean approved = data.getBooleanExtra("transaction_approved", false);

        if (status == TransactionProcessingResult.TRANSACTION_SUCCESS && approved) {
            String transactionRef = data.getStringExtra("transaction_reference");
            // Payment successful
        } else {
            // Payment failed or cancelled
        }
    }
}
```

## Supported Card Schemes

Visa, Mastercard, Maestro, UnionPay, JCB, Bancontact, TWINT (see `references/advanced.md`)

## Reading Order

1. `references/setup.md` — SDK setup first.
2. This file — card payment.
3. `references/refunds-voids.md` — handle refunds.

## See Also

- references/refunds-voids.md
- references/preauthorization.md
- https://developers.mypos.com/sdks/smart-sdk/responses
