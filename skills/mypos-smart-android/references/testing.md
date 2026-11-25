> AppMarket test guide: https://developers.mypos.com/guides/appmarket-guide/test-your-app

## Demo Debug POS Device (Required for Testing)

Standard production myPOS Smart devices **cannot run development or unsigned APKs**. To test your app you need a **Demo Debug POS device**:

- **How to get one**: Contact the myPOS sales team and request a Demo Debug POS device
- The device comes with a **myPOS Demo Card** — use this card to simulate payments during testing
- Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## Testing Workflow

1. Install your debug APK directly on the Demo Debug POS device via ADB
2. Open your app on the device
3. Use the myPOS Demo Card to simulate card payments
4. Verify payment results in `onActivityResult()`

## ADB Connection

```bash
adb devices                         # verify device is connected
adb install -r app-debug.apk        # install your debug APK
adb logcat | grep "mypos"           # monitor SDK logs
```

## What the Demo Card Tests

- Successful card payment
- Refund flow
- Void flow
- Pre-authorization

## Going Live (Production)

1. Sign your release APK
2. Upload to the Partner Portal: https://developers.mypos.com/guides/appmarket-guide/application-upload
3. Pass the app review: https://developers.mypos.com/guides/appmarket-guide/app-review
4. Distribute via AppMarket: https://developers.mypos.com/guides/appmarket-guide/distribute-an-app

## Reading Order

1. `references/partner-portal.md` — understand the full deployment lifecycle.
2. This file — test device setup.
3. `references/card-payment.md` — implement and test the payment flow.

## See Also

- references/partner-portal.md
- references/card-payment.md
