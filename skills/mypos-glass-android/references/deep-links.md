> Docs: https://developers.mypos.com/sdks/glass-sdk/deep-links

## Trigger a Payment from a Web Page

No Android SDK required — use a URI that opens the myPOS Glass app directly.

```html
<a href="mypos-glass://pay?amount=25.00&currency=EUR&reference=order-001&callbackScheme=myapp">
  Pay with myPOS Glass
</a>
```

Or via JavaScript:

```javascript
window.location.href =
  `mypos-glass://pay?amount=25.00&currency=EUR` +
  `&reference=order-${orderId}` +
  `&callbackScheme=myapp`;
```

## Trigger from Another Android App (Intent)

```java
String uri = "mypos-glass://pay" +
    "?amount=25.00" +
    "&currency=EUR" +
    "&reference=" + UUID.randomUUID() +
    "&callbackScheme=myapp";

Intent intent = new Intent(Intent.ACTION_VIEW, Uri.parse(uri));
startActivity(intent);
```

## Deep Link Parameters

| Parameter | Required | Description |
|-----------|----------|-------------|
| `amount` | Yes | Decimal amount (e.g. `25.00`) |
| `currency` | Yes | ISO 4217 code (e.g. `EUR`) |
| `reference` | Yes | Unique order reference |
| `callbackScheme` | No | Your app's URI scheme for the result callback |
| `description` | No | Payment description shown to customer |

## Receive the Result (App Callback)

Register your `callbackScheme` in `AndroidManifest.xml`:

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" />
    <data android:scheme="myapp" android:host="payment-result" />
</intent-filter>
```

## Reading Order

1. `references/setup.md` — Glass app must be installed on the device.
2. This file — deep link implementation.

## See Also

- references/setup.md
- https://developers.mypos.com/sdks/glass-sdk/deep-links
