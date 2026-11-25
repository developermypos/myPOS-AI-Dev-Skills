> Docs: https://developers.mypos.com/online-payments/payment-methods/store-card

## Store a Card (Get Token)

```php
use Mypos\IPC\IAStoreCard;
use Mypos\IPC\Card;
use Mypos\IPC\Defines;
use Mypos\IPC\IPC_Exception;

try {
    $card = new Card();
    $card->setCardType(Card::CARD_TYPE_VISA)
         ->setCardNumber('4111111111111111')
         ->setCardHolder('John Doe')
         ->setExpMM('12')
         ->setExpYY('26')
         ->setCvc('123');

    $storeCard = new IAStoreCard($config);
    $storeCard->setOrderID('STORE-' . uniqid())
              ->setCard($card);

    $response = $storeCard->process();

    if ($response->getStatus() == Defines::STATUS_SUCCESS) {
        $data = $response->getData(CASE_LOWER);
        $cardToken = $data['card_token'];
        // Persist $cardToken against the customer record
    }
} catch (IPC_Exception $e) { }
```

## Store Card via Redirect (with Purchase)

During a `Purchase`, request tokenization alongside payment:

```php
$purchase->setCardTokenRequest(Purchase::CARD_TOKEN_REQUEST_STORE_AND_PAY);
// card_token is returned in the IPN (urlNotify) webhook
```

## Update a Stored Card

```php
use Mypos\IPC\IAStoredCardUpdate;

$update = new IAStoredCardUpdate($config);
$update->setCardToken($existingToken)
       ->setCard($newCard);

$response = $update->process();
```

## Reading Order

1. `references/setup.md` — Config required.
2. This file — store card.
3. `references/server-side-payment.md` — charge the stored token.

## See Also

- references/server-side-payment.md
