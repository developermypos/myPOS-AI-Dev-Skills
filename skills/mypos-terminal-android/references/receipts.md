> Docs: https://developers.mypos.com/sdks/android-sdk/payments

## Set Default Receipt Configuration

In `Application.onCreate()` (applies to all transactions):

```java
POSHandler.setDefaultReceiptConfig(POSHandler.RECEIPT_PRINT_BOTH_MERCHANT_AND_CUSTOMER);
// Options: RECEIPT_NO_PRINT, RECEIPT_PRINT_ONLY_MERCHANT_COPY, RECEIPT_PRINT_BOTH_MERCHANT_AND_CUSTOMER
```

## Print a Custom Receipt Header/Footer

```java
posHandler.printCustomReceipt(
    activity,
    "Thank you for your purchase!\nwww.example.com",
    ReceiptPosition.FOOTER,
    new OnReceiptPrintListener() {
        @Override
        public void onComplete(boolean success) {
            // Done
        }
    }
);
```

## Print with Custom Font and Alignment

```java
List<ReceiptLine> lines = new ArrayList<>();
lines.add(new ReceiptLine("RECEIPT", Alignment.CENTER, FontSize.LARGE));
lines.add(new ReceiptLine("Order: #1001", Alignment.LEFT, FontSize.NORMAL));
lines.add(new ReceiptLine("Total: €25.00", Alignment.RIGHT, FontSize.NORMAL));

posHandler.printReceipt(activity, lines, listener);
```

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — receipt customization.

## See Also

- references/terminal-management.md
