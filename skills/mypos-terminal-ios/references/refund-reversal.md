> Docs: https://developers.mypos.com/sdks/ios-sdk

## Refund

**Objective-C:**
```objc
MPRefundRequest *refund = [MPRefundRequest requestWithTotal:@"25.00"
                                                     title:@"Refund #1001"
                                                  currency:MPCurrencyEUR];

[myPOSService requestRefund:refund
         fromViewController:self
                 completion:^(NSError *error) {
    if (!error) {
        MPTransactionData *tx = [myPOSService lastTranscationData];
    }
}];
```

**Swift:**
```swift
let refund = MPRefundRequest(total: NSDecimalNumber(string: "25.00"),
                             title: "Refund #1001",
                             currency: .EUR)

myPOSService.requestRefund(refund, from: self) { error in
    guard error == nil else { return }
}
```

## Refund with skipConfirmation

```objc
[myPOSService requestRefund:refund
         fromViewController:self
          skipConfirmation:YES
                 completion:^(NSError *error) { ... }];
```

## Reversal (Void Last Transaction)

```objc
[myPOSService requestReversalFromController:self
                           skipConfirmation:NO
                                 completion:^(NSError *error) {
    if (!error) { /* Reversal successful */ }
}];
```

## Reading Order

1. `references/payment.md` — the original payment.
2. This file — refund or reverse it.

## See Also

- references/payment.md
