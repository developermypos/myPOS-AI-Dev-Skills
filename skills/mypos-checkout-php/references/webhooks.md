> Docs: https://developers.mypos.com/online-payments/initial-setup-and-testing/ipn

## Handle an IPN (Webhook) Notification

```php
use Mypos\IPC\Response;
use Mypos\IPC\Defines;
use Mypos\IPC\IPC_Exception;

try {
    // $_POST contains the raw IPN data sent by myPOS
    $response = Response::getInstance(
        $config,
        $_POST,
        Defines::COMMUNICATION_FORMAT_POST
    );

    // ALWAYS verify signature before processing
    if (!$response->isSignatureCorrect()) {
        http_response_code(403);
        exit;
    }

    $data = $response->getData(CASE_LOWER);
    $status = (int) $data['status'];

    if ($status === Defines::STATUS_SUCCESS) {
        $orderID  = $data['orderid'];
        $trnref   = $data['ipc_trnref'];
        $amount   = $data['amount'];
        $currency = $data['currency'];
        $cardToken = $data['card_token'] ?? null;  // if tokenization was requested

        // Fulfill the order
    }

    // Respond with 200 — myPOS retries on other status codes
    http_response_code(200);

} catch (IPC_Exception $e) {
    // Log — invalid signature or config issue
    http_response_code(403);
}
```

## Key IPN Fields

| Field | Description |
|-------|-------------|
| `status` | `0` = success, non-zero = failure |
| `orderid` | Your original order ID |
| `ipc_trnref` | myPOS transaction reference |
| `amount` | Transaction amount |
| `currency` | ISO 4217 currency code |
| `card_token` | Card token (if requested) |

## Reading Order

1. `references/purchase.md` — set `setUrlNotify()`.
2. This file — validate the incoming IPN.

## See Also

- references/purchase.md
- https://developers.mypos.com/online-payments/initial-setup-and-testing/ipn
