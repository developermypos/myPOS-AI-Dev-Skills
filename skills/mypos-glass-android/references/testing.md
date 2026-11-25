> Testing guide: https://developers.mypos.com/sdks/glass-sdk/testing
> Going live: https://developers.mypos.com/sdks/glass-sdk/going-live
> Supported devices: https://developers.mypos.com/sdks/glass-sdk/devices

## Hardware Requirements

- **NFC-enabled Android device** running Android 8.0 or later
- **myPOS Glass app installed** (download from myPOS or Google Play)
- See the full list of supported devices: https://developers.mypos.com/sdks/glass-sdk/devices

## Demo Debug POS Device (for Testing)

To test without a live merchant account, request a **Demo Debug POS device** from the myPOS sales team. The device comes with:
- A pre-installed test version of the myPOS Glass app
- A **myPOS Demo Card** for simulating card payments

Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## Test Payments

Use the myPOS Demo Card on the NFC reader to simulate:
- Successful Visa payment
- Successful Mastercard payment
- Refund flow
- Void flow

Real cards are supported on production devices with a live merchant account.

## Going Live

1. Register as a myPOS merchant or partner
2. Install the production myPOS Glass app
3. Configure the Glass app with your merchant credentials
4. Follow the going-live guide: https://developers.mypos.com/sdks/glass-sdk/going-live

## Reading Order

1. `references/setup.md` — device and SDK setup.
2. This file — test configuration.
3. `references/card-payment.md` — run a test payment.

## See Also

- references/setup.md
- references/card-payment.md
