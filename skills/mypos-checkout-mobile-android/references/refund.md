> Docs: https://developers.mypos.com/in-app-payments/android-payments/refund

## Perform a Refund

Requires the Transaction Reference of the payment being refunded.

```java
Intent intent = new Intent(this, RefundActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_TRANSACTION_REFERENCE, transactionRef);
intent.putExtra(MyPos.INTENT_EXTRA_AMOUNT, 10.00f);
if (!orderId.equalsIgnoreCase("")) {
    intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, orderId);
}
startActivityForResult(intent, MyPos.REQUEST_CODE_REFUND);
```

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == MyPos.REQUEST_CODE_REFUND) {
        int status = data.getIntExtra(MyPos.INTENT_EXTRA_STATUS, MyPos.STATUS_INTERNAL_API_ERROR);
        if (status == MyPos.STATUS_SUCCESS) {
            float amount = data.getFloatExtra(MyPos.INTENT_EXTRA_AMOUNT, 0.00f);
            String currency = data.getStringExtra(MyPos.INTENT_EXTRA_CURRENCY);
            String refundRef = data.getStringExtra(MyPos.INTENT_EXTRA_TRANSACTION_REFERENCE);
        }
    }
}
```

Make sure you're using the correct Transaction Reference for the transaction being refunded.

## Reading Order

1. `references/purchase.md` — the original payment.
2. This file — refund it.
3. `references/transaction-status.md` — confirm the refund status if needed.

## See Also

- references/transaction-status.md
