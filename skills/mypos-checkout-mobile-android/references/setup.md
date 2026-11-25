> Docs: https://developers.mypos.com/in-app-payments/android-payments/integration
> Security & availability: https://developers.mypos.com/in-app-payments/android-payments/security-and-availability

## Add the Dependency

```groovy
// settings.gradle / project build.gradle
allprojects {
    repositories {
        mavenCentral()
    }
}
```

```groovy
// app/build.gradle
dependencies {
    implementation 'com.mypos:mobilepaymentssdk:1.2.8'
}
```

Requirements: Android Studio 2.0+, Android SDK Build Tools 25.0.0+, `minSdkVersion` 16 (Jelly Bean) or higher.

## Easy Setup (Production, Config Pack)

Use a single `configurationKey` generated in the merchant portal — no key files to manage.

```java
MyPos myPos = MyPos.getInstance();
myPos.init(configurationKey, "EUR", isSandbox);
myPos.setLanguage("EN");
```

## Advanced Setup (Sandbox / Testing)

```java
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    MyPos myPos = MyPos.getInstance();
    myPos.init(
        sid,                 // SID from the merchant portal
        walletNumber,         // Wallet Number
        "EUR",                // currency ISO code
        clientPrivateKey,     // client private key
        serverPublicKey,      // server public key
        true                  // isSandbox
    );
    myPos.setKeyIndex(1);
    myPos.setLanguage("EN");
}
```

Additional configuration:
- **Supported card networks** — default is Visa, Visa Electron, MasterCard, Maestro, VPay.
- **Address Verification Service (AVS)** — captures the cardholder's country and postcode as an
  extra security layer.

Communication between your app and myPOS is HTTPS with digitally signed requests/responses; card
data is captured and encrypted entirely inside the SDK's own Activities.

## Reading Order

1. This file — install and initialize.
2. Open the operation reference you need (see `references/README.md`).
3. `references/testing.md` — sandbox testing before going live.

## See Also

- references/README.md
- references/testing.md
