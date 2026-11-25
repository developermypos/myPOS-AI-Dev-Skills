---
name: mypos-terminal-ios
description: Use when a developer needs to control a myPOS POS terminal from an iOS application over Bluetooth
license: Apache-2.0
---

# myPOS iOS Bluetooth SDK (Terminal Control)

> Docs: https://developers.mypos.com/sdks/ios-sdk
> Integration guide: https://developers.mypos.com/in-person-payments/paying-counter

## Quick Decision Tree

```
iOS app controlling a myPOS terminal over Bluetooth?
│
├── First-time setup?
│   └── startInitializationFromController → references/initialization.md
│
├── Accept a card payment?
│   ├── With confirmation screen → checkoutWithRequest
│   ├── Skip amount confirm → checkoutWithRequest:skipConfirmation:YES
│   └── No modal (HUD only) → quickCheckoutWithRequest
│       → references/payment.md
│
├── Refund → requestRefund → references/refund-reversal.md
│
├── Void / reversal → requestReversalFromController → references/refund-reversal.md
│
├── Receipt or reprint?
│   └── printReceipt / reprintLastReceipt → references/receipts.md
│
├── Terminal lifecycle?
│   └── requestActivateTerminal / requestUpdateTerminal / requestDeactivateTerminal
│       → references/terminal-management.md
│
└── App config / terminal info?
    └── setAppName / setReceiptType / setPreferredLanguage / terminalId
        → references/configuration.md
```

## Start Here

1. Drag `myPOSBluetoothSDK.xcframework` into your Xcode project (Embed & Sign).
2. Link `ExternalAccessory.framework` under *Link Binary With Libraries*.
3. Add `NSBluetoothPeripheralUsageDescription` to `Info.plist`.
4. Swift projects: create a bridging header and add `#import <myPOSBluetoothSDK/myPOSBluetoothSDK.h>`.
5. Call `[myPOSService startInitializationFromController:self withCompletion:^(...)]` — all subsequent operations require successful initialization.
6. Read `references/setup.md`, then `references/initialization.md`.

## Non-Negotiable Rules

- **All SDK operations are asynchronous via completion blocks.** Never ignore the `NSError *error` parameter — `nil` means success, non-nil means failure.
- **Error code `-1` means the terminal disconnected.** Prompt the user to reconnect and re-initialize.
- **`NSBluetoothPeripheralUsageDescription` must be in `Info.plist`** or Apple will reject the app at review.
- **Swift callers must use a bridging header.** The SDK is Objective-C only — there is no native Swift module.
- **Card data is never exposed.** Only masked card numbers appear in `MPTransactionData`. Never attempt to read raw card numbers.
- **Demo Debug POS device required for testing** — request one from the myPOS sales team.

## Implementation Workflow

1. Add XCFramework + link ExternalAccessory + add Info.plist key
2. (Swift) Add bridging header
3. Call `startInitializationFromController:withCompletion:` — store nothing on failure
4. Build `MPCheckoutRequest` with total, title, currency
5. Call `checkoutWithRequest:fromViewController:completion:`
6. In completion block: check `error == nil`, then read `[myPOSService lastTranscationData]`

## Required Response Contract

When helping implement this SDK, always:
- Show both Objective-C and Swift versions of the key call
- Show the bridging header requirement for Swift
- Show `nil` error check in every completion block
- Remind the developer about `NSBluetoothPeripheralUsageDescription`
- Show `MPTransactionData` usage for reading transaction results

## Validation Checklist

- [ ] XCFramework added with "Embed & Sign"
- [ ] `ExternalAccessory.framework` linked
- [ ] `NSBluetoothPeripheralUsageDescription` in Info.plist
- [ ] Bridging header created for Swift projects
- [ ] Every completion block checks `error == nil`
- [ ] Disconnection (`error.code == -1`) handled with reconnect prompt
- [ ] Demo Debug POS device used for testing

## References

See `references/README.md` for the full index and reading order.
