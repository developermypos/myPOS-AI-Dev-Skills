> Docs: https://developers.mypos.com/sdks/glass-sdk/api-reference/integration

## Prerequisites

The **myPOS Glass app must be installed** on the Android device before your app can trigger payments. This is a hard requirement — without it, the SDK calls will fail.

## Add the Maven Dependency

In your app's `build.gradle`:

```gradle
dependencies {
    implementation 'com.mypos:glasssdk:1.0.6'
}
```

## Required Permissions

In `AndroidManifest.xml`:

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

NFC and card handling are managed entirely by the myPOS Glass app — no NFC permissions needed in your app.

## Verify Glass App is Installed (Optional Check)

```java
private boolean isGlassInstalled(Context context) {
    try {
        context.getPackageManager().getPackageInfo("com.mypos.glass", 0);
        return true;
    } catch (PackageManager.NameNotFoundException e) {
        return false;
    }
}
```

## Supported Devices

NFC-enabled Android devices running Android 8.0+. See the full device list:
https://developers.mypos.com/sdks/glass-sdk/devices

## Reading Order

1. This file — SDK setup and prerequisites.
2. `references/card-payment.md` — first payment.
3. `references/testing.md` — device requirements and going live.

## See Also

- references/card-payment.md
- references/testing.md
