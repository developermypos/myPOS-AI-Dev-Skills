> Docs: https://developers.mypos.com/sdks/android-sdk/integration
> Troubleshooting: https://developers.mypos.com/guides/troubleshooting-guides/terminal-sdk-and-api-integration

## Demo Debug POS Device (Required for Testing)

Production myPOS terminals require an active merchant account and cannot be used for development testing without configuration. To test your integration:

- **Request a Demo Debug POS device** from the myPOS sales team
- The device comes with a **myPOS Demo Card** for simulating payments
- Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## Connection Testing

```bash
# Verify ADB connection to your Android master device
adb devices

# Install and run your debug APK on the master device
adb install -r app-debug.apk
```

## Bluetooth Pairing

1. Put the Demo Debug POS terminal in pairing mode
2. Pair it with your Android master device via system Bluetooth settings
3. Use the terminal serial number to connect via `POSHandler.connectDevice()`

## Test Scenarios with Demo Card

- Successful payment
- Refund
- Void (same-day cancellation)
- Pre-authorization + completion
- Gift card activation

## Going Live

1. Register the production terminal with a live merchant account
2. Use production credentials (Partner Portal: https://partners.mypos.com/en)
3. Test on a production terminal before going live

## Reading Order

1. `references/setup.md` — SDK setup.
2. `references/connection.md` — terminal connection.
3. This file — test setup.
4. `references/payment.md` — run a test payment.

## See Also

- references/setup.md
- references/connection.md
