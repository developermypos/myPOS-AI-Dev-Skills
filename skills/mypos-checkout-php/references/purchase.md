> Docs: https://developers.mypos.com/online-payments/payment-methods/purchase

## Redirect Checkout (Standard Flow)

```php
use Mypos\IPC\Purchase;
use Mypos\IPC\Cart;
use Mypos\IPC\Customer;
use Mypos\IPC\IPC_Exception;

try {
    $cart = new Cart();
    $cart->add('Product Name', 1, 19.99);   // (name, qty, price)
    $cart->add('Shipping', 1, 4.99);

    $customer = new Customer();
    $customer->setEmail('buyer@example.com')
             ->setFirstName('John')->setLastName('Doe')
             ->setCountry('BG')->setCity('Sofia')
             ->setZip('1000')->setAddress('123 Main St');

    $purchase = new Purchase($config);
    $purchase->setOrderID('ORDER-' . uniqid())   // must be unique
             ->setCurrency('EUR')
             ->setCart($cart)
             ->setCustomer($customer)
             ->setUrlOk('https://myshop.com/success')
             ->setUrlCancel('https://myshop.com/cancel')
             ->setUrlNotify('https://myshop.com/webhook')
             ->setCardTokenRequest(Purchase::CARD_TOKEN_REQUEST_NONE)
             ->process();  // outputs auto-submitting form and exits

} catch (IPC_Exception $e) {
    // Log and show error page
}
```

`process()` sends a redirect — nothing after it executes.

## Return Parameters (at urlOk / urlCancel)

`$_GET['status']` — transaction status code (`0` = success)
`$_GET['ipc_trnref']` — transaction reference

## Reading Order

1. `references/setup.md` — Config must be ready first.
2. This file — redirect checkout.
3. `references/webhooks.md` — handle the IPN at `urlNotify`.

## See Also

- references/webhooks.md
- references/store-card.md
