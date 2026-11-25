> Docs: https://developers.mypos.com/sdks/smart-sdk/barcode-scanner

## Scan a Barcode or QR Code

```java
private static final int SCANNER_REQUEST_CODE = 1009;

MyPOSAPI.openBarcodeScannerActivity(this, SCANNER_REQUEST_CODE);
```

## Receive the Scan Result

```java
@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == SCANNER_REQUEST_CODE && resultCode == RESULT_OK && data != null) {
        String barcodeData = data.getStringExtra("barcode_data");
        String barcodeFormat = data.getStringExtra("barcode_format"); // e.g. "QR_CODE", "EAN_13"
        // Use the scanned value in your app
    }
}
```

## Reading Order

1. `references/setup.md` — SDK setup.
2. This file — scanner integration.

## See Also

- references/card-payment.md
