---
name: mypos-checkout-php
description: Use when a developer needs to accept payments in a PHP web application using the myPOS Online Checkout PHP SDK
license: Apache-2.0
---

# myPOS Online Checkout — PHP SDK

> Docs: https://developers.mypos.com/online-payments
> PHP SDK reference: https://developers.mypos.com/online-payments/sdks/php-sdk

## Quick Decision Tree

```
PHP web integration?
│
├── Customer redirected to myPOS checkout page?
│   └── Purchase class → references/purchase.md
│
├── Charge card directly (server-to-server)?
│   ├── New card → IAPurchase → references/server-side-payment.md
│   └── Stored token → IAPurchaseWithToken → references/server-side-payment.md
│
├── Hold funds first, capture later?
│   ├── Redirect flow → PreAuthorization → references/preauthorization.md
│   └── Server-side → Authorization → references/authorization.md
│
├── Refund or cancel?
│   ├── Refund → Refund class → references/refund-reversal.md
│   └── Same-day cancel → Reversal class → references/refund-reversal.md
│
├── Store card for later use?
│   └── IAStoreCard → references/store-card.md
│
├── Recurring / mandate / transfers?
│   └── MandateManagement / SendMoney / RequestMoney → references/transfer-of-funds.md
│
└── Incoming payment notification (IPN/webhook)?
    └── Response::getInstance() → references/webhooks.md
```

## Start Here

1. Register a merchant account and get your Config Pack (base64) from the myPOS merchant portal, **or** obtain the individual key files (private key PEM, API public key, encrypt public key) and your SID + Wallet numbers.
2. Install the SDK: `composer require developermypos/mypos-checkout-sdk`
3. Read `references/setup.md` — Config initialisation is the foundation of every operation.
4. Choose the operation type from the decision tree and open the matching reference.
5. For webhook/IPN handling always read `references/webhooks.md` — signature validation is mandatory.

## Non-Negotiable Rules

- **Private key files must never be web-accessible.** Store them outside the document root or use environment variables to pass the path.
- **Always call `$response->isSignatureCorrect()`** before trusting any webhook/IPN data. Processing unsigned callbacks is a security vulnerability.
- **Always wrap SDK calls in `try { } catch (IPC_Exception $e)`** — the SDK throws on every validation or transport failure.
- **Sandbox does NOT accept real cards.** Use the official test cards at https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
- **Never log or store raw card data** (`Card` object contents). Only store the `ipc_trnref` and `card_token` returned by the API.
- **Unique `OrderID` per transaction.** The API rejects duplicate order IDs.

## Implementation Workflow

### Redirect Checkout (most common)
1. Build `Config` object with credentials (key files or config package)
2. Build `Cart` with line items; optionally build `Customer`
3. Instantiate `Purchase($config)`, set `OrderID`, `Currency`, `Cart`, URLs (`setUrlOk`, `setUrlCancel`, `setUrlNotify`)
4. Call `$purchase->process()` — this outputs an auto-submitting HTML form and calls `exit`
5. Customer is redirected to myPOS checkout; on completion they return to `urlOk` or `urlCancel`
6. Receive IPN at `urlNotify` — validate with `Response::getInstance()` + `isSignatureCorrect()`

### Server-to-Server Payment
1. Build `Config` + `Cart` + `Card` objects
2. Instantiate `IAPurchase($config)`, set params including the `Card`
3. Call `$response = $iapurchase->process()`
4. Check `$response->getStatus() == Defines::STATUS_SUCCESS`

## Required Response Contract

When helping implement this SDK, always:
- Show the full `Config` initialisation block (key paths or `loadConfigurationPackage`)
- Include the `try/catch IPC_Exception` wrapper
- Show `$response->getStatus()` check for server-side operations
- Show `$response->isSignatureCorrect()` check in webhook handler
- Reference the specific operation class (not a generic "use the SDK" answer)

## Validation Checklist

- [ ] Config key files are outside the web root (or using config package)
- [ ] `$config->validate()` is called before first use
- [ ] Every operation is wrapped in `try/catch (IPC_Exception $e)`
- [ ] Webhook handler calls `isSignatureCorrect()` before processing
- [ ] Order IDs are unique per transaction
- [ ] Sandbox testing uses test cards, not real cards
- [ ] `STATUS_SUCCESS` (0) is checked on every server-side response

## References

See `references/README.md` for the full index and reading order.
