> Docs: https://developers.mypos.com/online-payments/payment-methods/transfer-of-funds

## Register a Mandate (Recurring Agreement)

```php
use Mypos\IPC\MandateManagement;
use Mypos\IPC\Defines;
use Mypos\IPC\IPC_Exception;

try {
    $mandate = new MandateManagement($config);
    $mandate->setMandateReference('MANDATE-' . uniqid())
            ->setMandateText('Monthly subscription')
            ->setAction(MandateManagement::MANDATE_ACTION_REGISTER)
            ->setUrlOk('https://myshop.com/mandate-ok')
            ->setUrlCancel('https://myshop.com/mandate-cancel')
            ->setUrlNotify('https://myshop.com/webhook')
            ->process();  // redirects customer
} catch (IPC_Exception $e) { }
```

## Cancel a Mandate

```php
$mandate->setAction(MandateManagement::MANDATE_ACTION_CANCEL)
        ->setMandateReference($existingRef)
        ->process();
```

## Request Money (Charge Under Mandate)

```php
use Mypos\IPC\RequestMoney;

$requestMoney = new RequestMoney($config);
$requestMoney->setOrderID('ORDER-' . uniqid())
             ->setMandateReference($mandateRef)
             ->setAmount(29.99)
             ->setCurrency('EUR');

$response = $requestMoney->process();
if ($response->getStatus() == Defines::STATUS_SUCCESS) {
    // Payment collected
}
```

## Send Money (Account Transfer)

```php
use Mypos\IPC\SendMoney;

$send = new SendMoney($config);
$send->setOrderID('SEND-' . uniqid())
     ->setRecipientWallet($targetWallet)
     ->setAmount(50.00)
     ->setCurrency('EUR');

$response = $send->process();
```

## Reading Order

1. `references/setup.md` — Config required.
2. This file — mandates and transfers.
3. `references/webhooks.md` — receive mandate IPN.

## See Also

- references/webhooks.md
