> Docs: https://developers.mypos.com/sdks/android-sdk/integration

## Add the Maven Dependency

In your app's `build.gradle`:

```gradle
dependencies {
    implementation 'com.mypos:slavesdk:2.1.9'
}
```

## Required Permissions

In `AndroidManifest.xml`:

```xml
<!-- For Bluetooth connection -->
<uses-permission android:name="android.permission.BLUETOOTH" />
<uses-permission android:name="android.permission.BLUETOOTH_ADMIN" />
<uses-permission android:name="android.permission.BLUETOOTH_CONNECT" />
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />

<!-- For network (TCP-IP) connection -->
<uses-permission android:name="android.permission.INTERNET" />
```

## Initialize POSHandler (Application Class)

```java
public class MyApp extends Application {
    @Override
    public void onCreate() {
        super.onCreate();

        POSHandler.setCurrency(Currency.EUR);
        POSHandler.setApplicationContext(this);
        POSHandler.setDefaultReceiptConfig(POSHandler.RECEIPT_PRINT_ONLY_MERCHANT_COPY);
    }
}
```

## Reading Order

1. This file — SDK setup.
2. `references/connection.md` — connect to the terminal.
3. `references/payment.md` — initiate a payment.

## See Also

- references/connection.md
