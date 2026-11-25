> Docs: https://developers.mypos.com/in-app-payments/ios-payments/integration
> Security & availability: https://developers.mypos.com/in-app-payments/ios-payments/security-availability

## Embed the Framework

Drag `MPCheckout.xcframework` into your Xcode project and set it to **Embed & Sign** under
*Frameworks, Libraries, and Embedded Content*. There is no CocoaPods, Swift Package Manager, or
Carthage distribution for this SDK. The framework's actual `MinimumOSVersion` is 11.0. It ships
with a module map, so `import MPCheckout` works directly in Swift — no bridging header is needed.

Requirements: Xcode 8+ (current framework builds require a recent Xcode toolchain to link against).

## Easy Setup (Production, Config Pack)

```Swift
MPCheckout.initialize(in: .EUR, withConfigurationKey: "eyJzaWQiOi...IjoiMyJ9")
```

```ObjectiveC
[MPCheckout initializeInCurrency:MPCurrencyEUR withConfigurationKey:@"eyJzaWQiOi...IjoiMyJ9"];
```

## Advanced Setup (Sandbox / Testing)

1. Convert the certificate and export the private key from the merchant portal's key-generation page:
   ```bash
   openssl x509 -outform der -in public_cert.txt -out public_cert.der
   ```
2. Add both files to the project via *Build Phases → Copy Bundle Resources*.
3. Initialize with the account number and store (Checkout) ID:

```Swift
MPCheckout.initialize(accountNumber: "1234567890",
                      storeId: "12345",
                      currency: .eur,
                      certificate: "public_cert",
                      privateKey: "private_key")
```

```ObjectiveC
[MPCheckout initializeWithAccountNumber:@"1234567890"
                                storeId:@"12345"
                               currency:MPCurrencyEUR
                            certificate:@"public_cert"
                             privateKey:@"private_key"
                                 bundle:[NSBundle mainBundle]
                               keyIndex:1];
```

In Swift, `bundle` and `keyIndex` are optional (default to the main bundle and key index 1); in
Objective-C they must be set explicitly.

Additional configuration:
- **Supported card networks** — default is Visa, Visa Electron, MasterCard, Maestro, VPay.
- **Address Verification Service (AVS)** — captures the cardholder's country and postcode as an
  extra security layer.

> **Note:** The public docs sometimes call this same class `MobilePaymentSDK` instead of
> `MPCheckout` (e.g. `MobilePaymentSDK.initialize(...)`, `MobilePaymentSDK.refundTransaction(...)`).
> This is the same SDK — use whichever class name your bundled framework version exposes.

Communication between your app and myPOS is HTTPS with digitally signed requests/responses; card
data is captured and encrypted entirely inside the SDK's own view controllers.

## Reading Order

1. This file — embed the framework and initialize.
2. Open the operation reference you need (see `references/README.md`).
3. `references/testing.md` — sandbox testing before going live.

## See Also

- references/README.md
- references/testing.md
