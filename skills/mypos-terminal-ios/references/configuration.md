> Docs: https://developers.mypos.com/sdks/ios-sdk

## Set App Name (Navigation Bar Title)

```objc
[myPOSService setAppName:@"My Store"];
// Shows "My Store" in the SDK's navigation bar instead of the default
```

## Set Receipt Type

```objc
[myPOSService setReceiptType:MPDeviceReceiptPrintAutomatically];
```

| MPDeviceReceipt Constant | Behaviour |
|--------------------------|-----------|
| `PrintAutomatically` | Print receipt without asking |
| `PrintAfterConfirmation` | Ask customer before printing |
| `PrintOnlyMerchantCopy` | Print merchant copy only |
| `DoNotPrint` | No printing |
| `Electron` | E-receipt for non-printer devices |

## Set Terminal Language

```objc
[myPOSService setPreferredLanguage:MPLanguageEnglish];
```

Available: English, Bulgarian, Italian, Croatian, Spanish, German, French, Romanian, Greek, Dutch, Latvian, Swedish, Portuguese, Icelandic, Slovenian, Czech, Lithuanian, Polish, Hungarian.

## Get Terminal ID

```objc
NSString *tid = [myPOSService terminalId];
```

## Check Printer Availability

```objc
BOOL hasPrinter = [myPOSService hasPrinter];
if (!hasPrinter) {
    // Fall back to e-receipt
    [myPOSService setReceiptType:MPDeviceReceiptElectron];
}
```

## Get Last Transaction Data

```objc
MPTransactionData *tx = [myPOSService lastTranscationData];
// tx.terminalId, tx.cardNumber (masked), tx.stan, tx.rrn, tx.reference
```

**Swift:**
```swift
let tx = myPOSService.lastTranscationData()
```

## Reading Order

1. `references/initialization.md` — connect first.
2. This file — configure the SDK before processing payments.
3. `references/payment.md` — process payments.

## See Also

- references/receipts.md
- references/terminal-management.md
