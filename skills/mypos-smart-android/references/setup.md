> Docs: https://developers.mypos.com/sdks/smart-sdk/integration

## Add the Maven Dependency

In your app's `build.gradle`:

```gradle
dependencies {
    implementation 'com.mypos:mypossmartsdk:1.0.8'
}
```

## Required Permissions

In `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

The myPOS Smart SDK communicates with the payment core via IPC — no additional Bluetooth or NFC permissions are required from your app.

## Bind to the myPOS Service

In your `Application` class:

```java
public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();
        MyPOSAPI.registerApplication(this, new OnBindListener() {
            @Override
            public void onBind() {
                // SDK is ready — proceed with payments
            }
        });
    }
}
```

## Get POS Info (Optional but Recommended)

```java
MyPOSAPI.getPOSInfo(activity, new OnPOSInfoListener() {
    @Override
    public void onReceive(POSInfo posInfo) {
        String tid = posInfo.getTerminalID();
        String currency = posInfo.getCurrency();
        String merchantName = posInfo.getMerchantName();
    }
});
```

## Reading Order

1. `references/partner-portal.md` — understand deployment requirements first.
2. This file — SDK setup.
3. `references/card-payment.md` — first payment implementation.

## See Also

- references/card-payment.md
- https://developers.mypos.com/sdks/smart-sdk/receive-pos-info
