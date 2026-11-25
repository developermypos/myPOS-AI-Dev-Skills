> Docs: https://developers.mypos.com/online-payments/payment-methods/preauthorization

## Pre-Authorize (Redirect Flow)

```php
use Mypos\IPC\PreAuthorization;
use Mypos\IPC\Cart;
use Mypos\IPC\IPC_Exception;

try {
    $cart = new Cart();
    $cart->add('Hotel Stay', 1, 200.00);

    $preauth = new PreAuthorization($config);
    $preauth->setOrderID('PREAUTH-' . uniqid())
            ->setCurrency('EUR')
            ->setCart($cart)
            ->setUrlOk('https://myshop.com/preauth-success')
            ->setUrlCancel('https://myshop.com/preauth-cancel')
            ->setUrlNotify('https://myshop.com/webhook')
            ->process();  // redirects customer, exits
} catch (IPC_Exception $e) { }
```

## Complete Pre-Authorization

```php
use Mypos\IPC\PreAuthorizationCompletion;

$completion = new PreAuthorizationCompletion($config);
$completion->setTrnref($preauthTrnref)
           ->setAmount(180.00)
           ->setCurrency('EUR');

$response = $completion->process();
if ($response->getStatus() == Defines::STATUS_SUCCESS) {
    // Capture successful
}
```

## Reading Order

1. `references/setup.md` — Config required.
2. This file — pre-authorization.
3. `references/webhooks.md` — IPN at urlNotify.

## See Also

- references/webhooks.md
- references/authorization.md
