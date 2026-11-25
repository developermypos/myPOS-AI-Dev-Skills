> Docs: https://developers.mypos.com/sdks/ios-sdk

## Activate Terminal (First-Time Setup)

```objc
[myPOSService requestActivateTerminalFromController:self
                                    withCompletion:^(NSError *error) {
    if (!error) {
        // Terminal activated — an activation code entry was shown on the device
    }
}];
```

**Swift:**
```swift
myPOSService.requestActivateTerminal(from: self) { error in
    guard error == nil else { return }
}
```

## Update Terminal Firmware

```objc
[myPOSService requestUpdateTerminalFromController:self
                                  withCompletion:^(NSError *error) {
    if (!error) {
        // Terminal is up to date or update was applied
    }
}];
```

## Deactivate Terminal

```objc
[myPOSService requestDeactivateTerminalFromController:self
                                     withCompletion:^(NSError *error) {
    if (!error) {
        // Terminal deactivated — a deactivation code was required on the device
    }
}];
```

## Get Terminal ID

```objc
NSString *tid = [myPOSService terminalId];
```

## Reading Order

1. `references/initialization.md` — connect first.
2. This file — terminal lifecycle.

## See Also

- references/initialization.md
- references/configuration.md
