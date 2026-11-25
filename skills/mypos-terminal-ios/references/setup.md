> Docs: https://developers.mypos.com/sdks/ios-sdk

## Add the XCFramework

1. Drag `myPOSBluetoothSDK.xcframework` into your Xcode project
2. In **Target → General → Frameworks, Libraries, and Embedded Content** set it to **Embed & Sign**
3. Under **Target → Build Phases → Link Binary With Libraries** also add `ExternalAccessory.framework`

## Required Info.plist Entry

```xml
<key>NSBluetoothPeripheralUsageDescription</key>
<string>This app uses Bluetooth to communicate with the payment terminal.</string>
```

Missing this key causes App Store rejection.

## Swift — Bridging Header

The SDK is Objective-C only. For Swift projects:

1. Create `MyApp-Bridging-Header.h`
2. Add: `#import <myPOSBluetoothSDK/myPOSBluetoothSDK.h>`
3. Set **Build Settings → Swift Compiler → Objective-C Bridging Header** to the file path

## Objective-C Import

```objc
#import <myPOSBluetoothSDK/myPOSBluetoothSDK.h>
```

## Minimum Requirements

- iOS 6.0+, Xcode 7+
- Bluetooth-capable iPhone or iPad
- myPOS merchant account

## Reading Order

1. This file — SDK setup.
2. `references/initialization.md` — connect to terminal.
3. `references/payment.md` — first payment.

## See Also

- references/initialization.md
