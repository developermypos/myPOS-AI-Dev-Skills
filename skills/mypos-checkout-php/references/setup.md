> Docs: https://developers.mypos.com/online-payments/sdks/php-sdk

## Install the SDK

```bash
composer require developermypos/mypos-checkout-sdk
```

Or manually: include `IPC/Loader.php` and it registers an SPL autoloader for the `Mypos\IPC\` namespace.

## Option A — Config with Key Files (recommended)

```php
use Mypos\IPC\Config;
use Mypos\IPC\IPC_Exception;

try {
    $config = new Config();
    $config->setPrivateKeyPath('/var/keys/private.pem')       // RSA private key — OUTSIDE web root
           ->setAPIPublicKeyPath('/var/keys/api_public.pem')  // myPOS API public key
           ->setEncryptPublicKeyPath('/var/keys/api_public.pem')
           ->setSid(12345)                                     // Store ID (numeric)
           ->setWallet(67890)                                  // Wallet / checkout number
           ->setKeyIndex(1)
           ->setVersion('1.4')
           ->setLang('en')
           ->validate();
} catch (IPC_Exception $e) {
    // Missing or invalid config — fix before proceeding
}
```

## Option B — Config Package (easier)

```php
$config = new Config();
$config->loadConfigurationPackage($base64EncodedPackage);
// Config package comes from the myPOS merchant portal
// Automatically sets: sid, wallet, privateKey, APIPublicKey, encryptPublicKey, keyIndex
```

## v1.4.1 — Additional Required Fields

```php
$config->setApplicationID('your-app-id')
       ->setPartnerID('your-partner-id');
```

## Reading Order

1. This file — SDK setup and Config.
2. `references/purchase.md` — first payment.

## See Also

- references/purchase.md
- references/webhooks.md
