> Docs: https://developers.mypos.com/in-app-payments/android-payments/payment
> Stored-card payment: https://developers.mypos.com/in-app-payments/android-payments/payments-with-stored-card

## Perform a Payment

```java
ArrayList<CartItem> cartItems = ...;

Intent intent = new Intent(this, PurchaseActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_CART_ITEMS, cartItems);
intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, UUID.randomUUID().toString());
startActivityForResult(intent, MyPos.REQUEST_CODE_PURCHASE);
```

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == MyPos.REQUEST_CODE_PURCHASE) {
        int status = data.getIntExtra(MyPos.INTENT_EXTRA_STATUS, MyPos.STATUS_INTERNAL_API_ERROR);
        if (status == MyPos.STATUS_SUCCESS) {
            String tranRef = data.getStringExtra(MyPos.INTENT_EXTRA_TRANSACTION_REFERENCE);
        }
    }
}
```

## Payment with a Stored Card

Same flow, plus the saved card's token:

```java
Intent intent = new Intent(this, PurchaseActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_CART_ITEMS, cartItems);
intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, UUID.randomUUID().toString());
intent.putExtra(MyPos.INTENT_EXTRA_CARD_TOKEN, storedCardToken);
startActivityForResult(intent, MyPos.REQUEST_CODE_PURCHASE);
```

Result handling is identical — check `INTENT_EXTRA_STATUS` before reading
`INTENT_EXTRA_TRANSACTION_REFERENCE`.

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — purchase and stored-card payment.
3. `references/transaction-status.md` — confirm status if needed.

## See Also

- references/store-card.md
- references/refund.md
- references/transaction-status.md
