> Docs: https://developers.mypos.com/online-payments/payment-methods/authorization

## Server-Side Authorization (Direct, with Card Token)

```php
use Mypos\IPC\Authorization;
use Mypos\IPC\Cart;
use Mypos\IPC\Defines;
use Mypos\IPC\IPC_Exception;

try {
    $cart = new Cart();
    $cart->add('Subscription', 1, 29.99);

    $auth = new Authorization($config);
    $auth->setOrderID('AUTH-' . uniqid())
         ->setCurrency('EUR')
         ->setCart($cart)
         ->setCardToken($storedToken);

    $response = $auth->process();

    if ($response->getStatus() == Defines::STATUS_SUCCESS) {
        $data = $response->getData(CASE_LOWER);
        $authTrnref = $data['ipc_trnref'];
        // Store authTrnref for capture
    }
} catch (IPC_Exception $e) { }
```

## Capture an Authorization

```php
use Mypos\IPC\AuthorizationCapture;

$capture = new AuthorizationCapture($config);
$capture->setTrnref($authTrnref)
        ->setAmount(29.99)
        ->setCurrency('EUR');

$response = $capture->process();
if ($response->getStatus() == Defines::STATUS_SUCCESS) {
    // Funds captured
}
```

## Reading Order

1. `references/store-card.md` — get a card token first.
2. This file — authorization and capture.
3. `references/refund-reversal.md` — reverse if needed.

## See Also

- references/store-card.md
- references/preauthorization.md
