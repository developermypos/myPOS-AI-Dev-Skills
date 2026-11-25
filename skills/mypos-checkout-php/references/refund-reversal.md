> Docs: https://developers.mypos.com/online-payments/payment-methods/refund

## Refund a Transaction

```php
use Mypos\IPC\Refund;
use Mypos\IPC\IPC_Exception;

try {
    $refund = new Refund($config);
    $refund->setTrnref($originalTrnref)   // ipc_trnref from the original transaction
           ->setAmount(19.99)
           ->setCurrency('EUR');

    $result = $refund->process();  // returns true if successful

    if ($result === true) {
        // Refund accepted
    }
} catch (IPC_Exception $e) { }
```

The `Refund::process()` method returns `true` (not a `Response` object) when the refund is accepted — it internally validates amount, currency, and signature.

## Reverse a Transaction (Same-Day Void)

```php
use Mypos\IPC\Reversal;

try {
    $reversal = new Reversal($config);
    $reversal->setTrnref($originalTrnref);

    $response = $reversal->process();

    if ($response->getStatus() == Defines::STATUS_SUCCESS) {
        // Transaction reversed
    }
} catch (IPC_Exception $e) { }
```

## Reading Order

1. `references/purchase.md` or `references/server-side-payment.md` — the original transaction.
2. This file — refund or reverse it.

## See Also

- references/purchase.md
