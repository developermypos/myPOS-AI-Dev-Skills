> Docs: https://developers.mypos.com/sdks/ios-sdk

## Standard Checkout (with Confirmation Screen)

**Objective-C:**
```objc
MPCheckoutRequest *req = [MPCheckoutRequest requestWithTotal:@"25.00"
                                                      title:@"Order #1001"
                                                   currency:MPCurrencyEUR];
[req setTransactionReference:@"order-1001"];  // optional, max 20 chars

[myPOSService checkoutWithRequest:req
              fromViewController:self
                      completion:^(NSError *error) {
    if (error) {
        // error.code == -1 means terminal disconnected
        return;
    }
    MPTransactionData *tx = [myPOSService lastTranscationData];
    NSString *masked = tx.cardNumber;  // masked PAN, e.g. ****1234
    NSString *ref = tx.reference;
}];
```

**Swift:**
```swift
let req = MPCheckoutRequest(total: NSDecimalNumber(string: "25.00"),
                            title: "Order #1001",
                            currency: .EUR)

myPOSService.checkout(with: req, from: self) { error in
    guard error == nil else { return }
    let tx = myPOSService.lastTranscationData()
}
```

## Skip Amount Confirmation

```objc
[myPOSService checkoutWithRequest:req
              fromViewController:self
              skipConfirmation:YES
                      completion:^(NSError *error) { ... }];
```

## Quick Checkout (No Modal, HUD Overlay Only)

```objc
[myPOSService quickCheckoutWithRequest:req
                    fromViewController:self
                            completion:^(NSError *error) { ... }];
```

## Supported Currencies (MPCurrency)

EUR, USD, GBP, CHF, BGN, RON, PLN, HUF, CZK, DKK, ISK, NOK, SEK, HRK

## Reading Order

1. `references/initialization.md` — connect first.
2. This file — payment.
3. `references/refund-reversal.md` — handle refunds.

## See Also

- references/refund-reversal.md
- references/receipts.md
