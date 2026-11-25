> Docs: https://developers.mypos.com/sdks/ios-sdk

## Build a Custom Receipt

```objc
MPReceiptData *receipt = [[MPReceiptData alloc] init];
[receipt addLogo:1];                         // logo index
[receipt addEmptyRow];
[receipt addRowWithText:@"Thank you!"
                aligned:MPReceiptAlignmentCenter
          withFontSize:MPReceiptFontSizeDouble];
[receipt addRowWithText:@"Order: #1001"
                aligned:MPReceiptAlignmentLeft
          withFontSize:MPReceiptFontSizeSingle];
[receipt addEmptyRow];
[receipt addRowWithText:@"www.example.com"
                aligned:MPReceiptAlignmentCenter
          withFontSize:MPReceiptFontSizeSingle];
```

## Print the Receipt

```objc
[myPOSService printReceipt:receipt completion:^(NSError *error) {
    if (!error) { /* Printed successfully */ }
}];
```

**Swift:**
```swift
myPOSService.printReceipt(receipt) { error in
    guard error == nil else { return }
}
```

## Reprint Last Transaction Receipt

```objc
[myPOSService reprintLastReceiptWithCompletion:^(NSError *error) {
    if (!error) { /* Reprinted */ }
}];
```

## Receipt Alignment Options (MPReceiptAlignment)

| Constant | Alignment |
|----------|-----------|
| `MPReceiptAlignmentLeft` | Left |
| `MPReceiptAlignmentCenter` | Center |
| `MPReceiptAlignmentRight` | Right |

## Reading Order

1. `references/payment.md` — complete a payment first.
2. This file — print receipt after payment.

## See Also

- references/configuration.md
