> Docs: https://developers.mypos.com/sdks/ios-sdk

## Initialize and Connect to Terminal

**Objective-C:**
```objc
[myPOSService startInitializationFromController:self
                               withCompletion:^(MPPOSDeviceMode mode, NSError *error) {
    if (error) {
        // Bluetooth unavailable or terminal not found
        return;
    }
    // mode: MPPOSDeviceModeStandard, MPPOSDeviceModeModern, or MPPOSDeviceModeUnknown
    // Ready to process payments
}];
```

**Swift:**
```swift
myPOSService.startInitialization(from: self) { mode, error in
    guard error == nil else { return }
    // mode is MPPOSDeviceMode
}
```

## Connect to a Specific Terminal

If the user has multiple terminals, set the preferred one by serial number:

```objc
[myPOSService setDefaultPOSDeviceSerialNumber:@"MYPOS-SERIAL-123"];
// Then call startInitializationFromController — it will auto-connect to this terminal
```

## Check Connection Status

```objc
[myPOSService isConnectedFromViewController:self completion:^(NSError *error) {
    BOOL connected = (error == nil);
}];
```

## Device Modes (MPPOSDeviceMode)

| Mode | Description |
|------|-------------|
| `MPPOSDeviceModeStandard` | Classic myPOS terminal |
| `MPPOSDeviceModeModern` | Newer terminal model |
| `MPPOSDeviceModeUnknown` | Detection failed |

## Reading Order

1. `references/setup.md` — framework and Info.plist first.
2. This file — Bluetooth initialization.
3. `references/payment.md` — first payment.

## See Also

- references/setup.md
- references/payment.md
