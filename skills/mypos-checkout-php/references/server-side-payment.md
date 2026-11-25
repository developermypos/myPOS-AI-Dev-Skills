> Docs: https://developers.mypos.com/online-payments/payment-methods/iapurchase

## Direct Card Charge (IAPurchase)

Server-to-server — customer never leaves your page.

```php
use Mypos\IPC\IAPurchase;
use Mypos\IPC\Cart;
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

    $cart = new Cart();
    $cart->add('Subscription', 1, 29.99);

    $iapurchase = new IAPurchase($config);
    $iapurchase->setOrderID('ORDER-' . uniqid())
               ->setCurrency('EUR')
               ->setCart($cart)
               ->setCard($card);

    $response = $iapurchase->process();

    if ($response->getStatus() == Defines::STATUS_SUCCESS) {
        $data = $response->getData(CASE_LOWER);
        $trnref = $data['ipc_trnref'];
        // Payment successful
    } else {
        $msg = $response->getStatusMsg();
        // Payment failed
    }
} catch (IPC_Exception $e) {
    // Connection or validation error
}
```

## Charge a Stored Card Token (IAPurchaseWithToken)

```php
use Mypos\IPC\IAPurchaseWithToken;

$tokenPurchase = new IAPurchaseWithToken($config);
$tokenPurchase->setOrderID('ORDER-' . uniqid())
              ->setCurrency('EUR')
              ->setCart($cart)
              ->setCardToken($storedToken);

$response = $tokenPurchase->process();
```

## Reading Order

1. `references/setup.md` — Config required.
2. This file — direct charge.
3. `references/store-card.md` — to get a card token first.

## See Also

- references/store-card.md
- references/refund-reversal.md
