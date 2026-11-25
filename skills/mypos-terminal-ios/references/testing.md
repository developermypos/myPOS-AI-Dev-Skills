> Docs: https://developers.mypos.com/sdks/ios-sdk
> Troubleshooting: https://developers.mypos.com/guides/troubleshooting-guides/terminal-sdk-and-api-integration

## Demo Debug POS Device (Required for Testing)

Production myPOS terminals require a live merchant account. For development testing:

- **Request a Demo Debug POS device** from the myPOS sales team
- The device ships with a **myPOS Demo Card** for simulating card payments
- Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## Bluetooth Pairing

1. Put the Demo Debug POS terminal in Bluetooth pairing mode (see device instructions)
2. Pair it with your iPhone or iPad via iOS Settings → Bluetooth
3. Call `startInitializationFromController:withCompletion:` — the SDK will auto-connect

## Test Scenarios with Demo Card

- Successful payment (checkoutWithRequest)
- Refund (requestRefund)
- Reversal (requestReversal)
- Custom receipt print (printReceipt)
- Reprint (reprintLastReceipt)

## Simulator Note

Bluetooth is not available in the iOS Simulator. Always test on a physical device connected to the Demo Debug POS terminal.

## Going Live

1. Register as a myPOS merchant
2. Replace Demo Debug POS device with a production terminal
3. Follow the Partner Portal onboarding: https://partners.mypos.com/en

## Reading Order

1. `references/setup.md` — SDK setup.
2. `references/initialization.md` — Bluetooth connection.
3. This file — test setup.
4. `references/payment.md` — run a test payment.

## See Also

- references/initialization.md
- references/payment.md
