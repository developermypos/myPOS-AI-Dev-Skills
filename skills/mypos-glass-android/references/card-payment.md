> Docs: https://developers.mypos.com/sdks/glass-sdk/api-reference/payments
> Responses: https://developers.mypos.com/sdks/glass-sdk/api-reference/responses

## Initiate a Card Payment

Only **Visa** and **Mastercard** are supported.

```java
private static final int PAYMENT_REQUEST_CODE = 2001;

MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .printMerchantReceipt(MyPOSUtil.RECEIPT_ON)
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

        if (approved) {
            String ref = data.getStringExtra("transaction_reference");
            // Payment successful
        }
    }
}
```

## Get Last Transaction Data

```java
MyPOSAPI.getLastTransactionData(this, new OnLastTransactionDataListener() {
    @Override
    public void onReceive(LastTransactionData data) {
        String ref = data.getTransactionReference();
    }
});
```

## Reading Order

1. `references/setup.md` — ensure Glass app is installed and SDK initialized.
2. This file — payment flow.
3. `references/refunds-voids.md` — handle refunds.

## See Also

- references/refunds-voids.md
- https://developers.mypos.com/sdks/glass-sdk/api-reference/get-last-transaction-data
