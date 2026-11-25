> Docs: https://developers.mypos.com/in-app-payments/android-payments/add-a-card

## Add a Card

```java
Intent intent = new Intent(this, StoreCardActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_VERIFICATION_AMOUNT, 0.05f);
// Optional: automatically refund the verification amount once the card is verified
intent.putExtra(MyPos.INTENT_EXTRA_AUTO_REVERSE, true);
startActivityForResult(intent, MyPos.REQUEST_CODE_STORE_CARD);
```

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (resultCode == RESULT_OK && requestCode == MyPos.REQUEST_CODE_STORE_CARD) {
        int status = data.getIntExtra(MyPos.INTENT_EXTRA_STATUS, MyPos.STATUS_INTERNAL_API_ERROR);
        if (status == MyPos.STATUS_SUCCESS) {
            StoredCardModel storedCard = data.getParcelableExtra(MyPos.INTENT_EXTRA_STORED_CARD);
            String cardToken = storedCard.getCardToken();
        }
    }
}
```

Some card issuers reject a €0.00 verification amount — use a small non-zero amount (e.g. 0.05) and
enable `INTENT_EXTRA_AUTO_REVERSE` to refund it automatically once the card is verified.

## Update a Stored Card

```java
Intent intent = new Intent(this, UpdateCardActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_CARD_TOKEN, existingCardToken);
intent.putExtra(MyPos.INTENT_EXTRA_VERIFICATION_AMOUNT, 0.05f);
startActivityForResult(intent, MyPos.REQUEST_CODE_UPDATE_CARD);
```

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — save or update a card.
3. `references/purchase.md` — charge the stored card.

## See Also

- references/purchase.md
- references/preauthorization.md
