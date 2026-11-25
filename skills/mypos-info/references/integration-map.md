# myPOS Integration Map

A reference card for all available myPOS developer integrations.

---

## In-Person Payments

### mypos-smart-android
- **Platform:** Android (app runs directly on the myPOS Smart terminal)
- **Language:** Kotlin / Java
- **What it does:** Your Android app runs on the myPOS Smart device and triggers card payments through the terminal's secure payment core via IPC. The app and the payment hardware are on the same device.
- **Best for:** Point-of-sale apps, table service, retail — anywhere you want a custom app UI running on the terminal itself.
- **Not for:** Controlling a terminal from a separate device; non-Android environments.
- **Key requirement:** App must be uploaded to the myPOS AppMarket via Partner Portal and pass app review before distribution.

### mypos-terminal-android
- **Platform:** Android (separate controller device)
- **Language:** Kotlin / Java
- **What it does:** Your Android phone, tablet, or device controls a separate myPOS terminal over Bluetooth, USB, or TCP/IP. The terminal handles all card operations; your app initiates them.
- **Best for:** Retail counter setups where the merchant device and the payment terminal are separate; mobile POS scenarios.
- **Not for:** Running directly on the myPOS Smart terminal; iOS or Windows controllers.

### mypos-terminal-ios
- **Platform:** iOS (separate controller device)
- **Language:** Objective-C / Swift
- **What it does:** Your iPhone or iPad controls a myPOS terminal over Bluetooth.
- **Best for:** iOS-based POS applications, hospitality, tableside ordering.
- **Not for:** Android controllers; non-Bluetooth connections.
- **Key requirement:** XCFramework integration, `NSBluetoothPeripheralUsageDescription` in Info.plist, bridging header for Swift.

### mypos-terminal-dotnet
- **Platform:** Windows / .NET
- **Language:** C# (.NET Framework or .NET Core)
- **What it does:** Your .NET application controls a myPOS terminal over USB/Serial or TCP/IP.
- **Best for:** Windows POS software, back-office integration, WinForms/WPF apps with COM port terminal connections.
- **Not for:** Mobile platforms; Android or iOS.
- **Key requirement:** `ProcessingFinished` event must be subscribed before any transaction call.

### mypos-glass-android
- **Platform:** Any NFC-enabled Android phone
- **Language:** Android SDK or deep links
- **What it does:** Turns a standard NFC-capable Android phone into a card-acceptance terminal via the myPOS Glass SoftPOS app.
- **Best for:** Delivery drivers, mobile vendors, any scenario where a dedicated terminal isn't feasible.
- **Not for:** Devices without NFC; iOS; card schemes other than Visa and Mastercard.
- **Key requirement:** myPOS Glass app must be installed on the device.

### mypos-ipp
- **Platform:** Any (wire protocol, no SDK required)
- **Language:** Any language capable of TCP/serial/USB I/O
- **What it does:** Direct binary protocol communication with myPOS terminals. All terminal SDKs (Android/iOS/.NET) are built on top of IPP. Gives full protocol-level control over all 31 terminal methods.
- **Best for:** Kiosks, vending machines, unattended terminals, Windows POS systems without the .NET SDK, custom embedded systems, languages with no myPOS SDK.
- **Not for:** Situations where a higher-level Terminal SDK exists for your platform and you don't need protocol control.

---

## In-App Payments (Native Mobile Checkout)

### mypos-checkout-mobile-android
- **Platform:** Android (your own app, not a myPOS device)
- **Language:** Kotlin / Java
- **What it does:** Embeds myPOS card payments directly inside your own native Android app via the
  SDK's own Activities (`PurchaseActivity`, `StoreCardActivity`, etc.). No redirect to a browser or
  hosted page; card data never touches your app code.
- **Best for:** Merchant-owned shopping, booking, or delivery apps that want in-app checkout without
  a dedicated payment terminal.
- **Not for:** Websites (use a Checkout skill instead).
- **Key requirement:** Production traffic requires emailing `integrations@mypos.com` for app review
  before going live.

### mypos-checkout-mobile-ios
- **Platform:** iOS (your own app, not a myPOS device)
- **Language:** Swift / Objective-C
- **What it does:** Embeds myPOS card payments directly inside your own native iOS app via the
  SDK's own view controllers (`MPPaymentViewController`, `MPStoreCardViewController`, etc.). No
  redirect to a browser or hosted page; card data never touches your app code.
- **Best for:** Merchant-owned shopping, booking, or delivery apps that want in-app checkout without
  a dedicated payment terminal.
- **Not for:** Websites (use a Checkout skill instead); Android apps (use `mypos-checkout-mobile-android`).
- **Key requirement:** `MPCheckout.xcframework` is manually embedded (no CocoaPods/SPM/Carthage);
  production traffic requires emailing `integrations@mypos.com` for app review before going live.

---

## Online Payments

### mypos-checkout-ts
- **Platform:** Server-side (Node.js)
- **Language:** TypeScript / JavaScript
- **What it does:** Managed SDK for accepting online card payments. Handles HMAC signing, form generation, and webhook validation. Customers are redirected to a myPOS-hosted checkout page.
- **Best for:** Node.js/TypeScript backends that want a managed SDK experience.
- **Not for:** Non-Node.js backends; embedded iFrame flows.

### mypos-checkout-php
- **Platform:** Server-side (PHP)
- **Language:** PHP (Composer)
- **What it does:** PHP SDK equivalent to mypos-checkout-ts. Handles signing, form generation, IPN validation.
- **Best for:** PHP backends (Laravel, Symfony, plain PHP).
- **Not for:** Non-PHP backends; iFrame flows.

### mypos-checkout-rest
- **Platform:** Server-side (any)
- **Language:** Any language with HTTP client support
- **What it does:** Direct HTTP integration with the myPOS Checkout API. Requires manual RSA-SHA256 request signing but works in any language or stack. Covers all checkout operations including server-to-server (stored card), pre-authorization, and mandate management.
- **Best for:** Backends in languages without a myPOS SDK; teams that want full control over HTTP communication.
- **Not for:** Situations where managing RSA signing is undesirable (use TS or PHP SDK instead).
- **Key requirement:** RSA private key must be stored outside the web root; `IPCPurchaseNotify` webhook must be signature-verified.

### mypos-checkout-embedded
- **Platform:** Web (browser)
- **Language:** JavaScript / any backend for session creation
- **What it does:** Embeds a myPOS payment form directly in your page as an iFrame — customer never leaves your site. Supports Mastercard, Visa, Apple Pay, Google Pay.
- **Best for:** E-commerce where redirect to a hosted page is undesirable; Apple/Google Pay.
- **Not for:** Non-web contexts; card schemes beyond Visa/Mastercard/Apple Pay/Google Pay.
- **Key requirement:** Container div minimum 320×620px; domain verification file required for Apple Pay.

---

## Backend APIs

### mypos-api-gateway-ts
- **Platform:** Server-side (Node.js)
- **Language:** TypeScript / JavaScript
- **What it does:** TypeScript SDK for the myPOS API Gateway. Covers Banking (account balances, statements, transaction history), eCommerce (payment links, buttons), and ePOS (remote terminal payments, refunds) APIs.
- **Best for:** Node.js/TypeScript backends that want a managed SDK with automatic token handling.
- **Not for:** Non-Node.js backends.

### mypos-api-gateway-rest
- **Platform:** Server-side (any)
- **Language:** Any language with HTTP client support
- **What it does:** Direct HTTP integration with the myPOS API Gateway. Same three APIs (Banking, eCommerce, ePOS) as the TS SDK but without a library — you manage OAuth tokens and headers manually.
- **Best for:** Any language or stack; backends that already handle OAuth token management.
- **Not for:** Situations where managing OAuth and 4-header sets per request is undesirable (use TS SDK instead).

---

## Device Model → Skill Quick Lookup

| You have... | App on device? | Skill |
|------------|---------------|-------|
| myPOS Smart, Smart N5, Carbon, mini5 | Yes (app runs ON terminal) | mypos-smart-android |
| myPOS Smart, Smart N5, Carbon, mini5 | No (separate controller) | mypos-terminal-android / ios / dotnet |
| myPOS Go, Combo, Hub | Android controller | mypos-terminal-android |
| myPOS Go, Combo, Hub | iOS controller | mypos-terminal-ios |
| myPOS Go, Combo, Hub | Windows/.NET controller | mypos-terminal-dotnet |
| myPOS Classic, Q20, Q20+, K300 | Low-level / no SDK | mypos-ipp |
| Any myPOS terminal, kiosk/vending | Direct protocol control | mypos-ipp |
| Standard NFC Android phone | SoftPOS via Glass app | mypos-glass-android |
| No terminal — online only | — | mypos-checkout-* or mypos-api-gateway-* |
| No terminal — payments inside your own app | Native Android app | mypos-checkout-mobile-android |
| No terminal — payments inside your own app | Native iOS app | mypos-checkout-mobile-ios |
