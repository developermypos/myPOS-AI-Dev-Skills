# Integration Comparisons

Use this reference when choosing between integrations that solve similar problems.

---

## Choosing a Terminal Integration

All Terminal SDKs (Android, iOS, .NET) are built on top of the IPP protocol. The SDKs handle TCP framing, SSL certificate management, stage polling, and platform-specific event handling so you don't have to.

**Rule:** Choose a Terminal SDK if one exists for your platform. Choose IPP directly only when no SDK covers your platform or you need protocol-level control.

| Scenario | Skill |
|----------|-------|
| Android app runs ON the myPOS Smart terminal | `mypos-smart-android` |
| Android phone / tablet controlling a terminal over Bluetooth / USB / TCP | `mypos-terminal-android` |
| iOS iPhone / iPad controlling a terminal over Bluetooth | `mypos-terminal-ios` |
| Windows .NET / C# controlling a terminal over USB, Serial, or TCP | `mypos-terminal-dotnet` |
| Any platform — direct binary protocol, no SDK | `mypos-ipp` |
| Kiosk / vending machine / headless / unattended terminal | `mypos-ipp` (VENDING_PURCHASE) |
| Testing at protocol level, debugging a Terminal SDK integration | `mypos-ipp` |

**mypos-smart-android vs mypos-terminal-android:** The distinction is whether your app runs _on_ the terminal or _beside_ it. Smart SDK: app and terminal are the same device. Terminal Android SDK: app is on a phone/tablet, terminal is a separate connected device.

**IPP vs Terminal SDK trade-offs:**

| | Terminal SDK | mypos-ipp |
|--|-------------|-----------|
| Setup complexity | Lower — SDK handles framing and SSL | Higher — you implement framing, SSL, stage loop |
| Platform flexibility | Only for the SDK's platform | Any platform |
| Protocol visibility | Hidden behind callbacks | Full visibility |
| Method coverage | SDK-specific subset | All 31 methods |
| Debugging | SDK logs + callbacks | DCM logs, full wire inspection |

---

## Choosing a Checkout Integration

Online payment integrations split along two axes: redirect vs embedded, and SDK vs direct REST.

**Redirect vs embedded:**
- **Redirect** (checkout-ts, checkout-php, checkout-rest): customer leaves your site and completes payment on a myPOS-hosted page, then returns. Minimal PCI scope — you never touch card data.
- **Embedded** (checkout-embedded): payment form renders as an iFrame inside your page. Customer never leaves. Required for Apple Pay and Google Pay.

**SDK vs direct REST:**
| | SDK (ts / php) | Direct REST (checkout-rest) |
|--|---------------|---------------------------|
| Signature computation | SDK handles it | You compute RSA-SHA256 manually |
| Language | TypeScript/Node.js (ts), PHP (php) | Any language |
| Setup effort | Lower | Higher |
| Flexibility | SDK API surface only | Full API access |

**Server-to-server charges (stored card, no user interaction):**
Always use `mypos-checkout-rest` (IPCIAPurchase with CardToken). The TS and PHP SDKs also support this via their IAPurchase classes, but direct REST gives the most flexibility for backend-only flows.

**When to use each:**

| Scenario | Skill |
|----------|-------|
| TypeScript / Node.js backend, redirect checkout | `mypos-checkout-ts` |
| PHP backend, redirect checkout | `mypos-checkout-php` |
| Any other language, redirect checkout | `mypos-checkout-rest` |
| iFrame form, no redirect, Apple Pay, Google Pay | `mypos-checkout-embedded` |
| Server-to-server charge (stored card token) | `mypos-checkout-rest` |
| Pre-authorization and capture | `mypos-checkout-rest` (or TS/PHP SDK) |
| Mandate management / recurring payments | `mypos-checkout-rest` |
| Send/request money between myPOS accounts | `mypos-checkout-rest` |

---

## Choosing a Mobile In-App Checkout Integration

`mypos-checkout-mobile-android`/`mypos-checkout-mobile-ios` are for embedding payments **inside a
native mobile app you own** — distinct from both the Checkout skills (web) and the Terminal SDKs
(card-present hardware).

**vs `mypos-checkout-embedded`:** Embedded Checkout renders an iFrame *inside a web page* (browser
context, works on any platform via WebView or a real browser). The Mobile Checkout SDKs render
*native* Activities/view controllers inside a *native* Android or iOS app — no WebView, no iFrame,
and no Apple Pay/Google Pay wallet buttons (those are an Embedded Checkout feature).

**vs Terminal SDKs (`mypos-terminal-android`/`-ios`/`-dotnet`, `mypos-smart-android`):** Terminal
SDKs control a **physical myPOS terminal** — the cardholder taps/inserts/swipes a card on real
hardware (card-present). The Mobile Checkout SDKs capture the card number directly inside the app's
own UI (card-not-present, like a checkout page) — there is no terminal involved at all.

| Scenario | Skill |
|----------|-------|
| Native Android app, card entered inside the app itself | `mypos-checkout-mobile-android` |
| Native iOS app, card entered inside the app itself | `mypos-checkout-mobile-ios` |
| Web page or WebView, iFrame form, Apple/Google Pay | `mypos-checkout-embedded` |
| Cardholder taps/inserts a card on a physical myPOS terminal | a Terminal SDK or `mypos-smart-android` |

---

## Choosing an API Gateway Integration

The API Gateway covers three sub-APIs: Banking (account balances, statements, transaction history), eCommerce (payment links, buttons, payment requests), and ePOS (remote terminal payments via API).

Both skills cover all three. The split is purely by language/platform:

| | mypos-api-gateway-ts | mypos-api-gateway-rest |
|--|---------------------|----------------------|
| Language | TypeScript / Node.js | Any language |
| OAuth token handling | SDK manages refresh automatically | You manage Bearer + session token lifecycle |
| Setup effort | Lower | Higher |

**Which sub-API do you need?**

| Goal | Sub-API |
|------|---------|
| Read account balances, transaction history, download statements | Banking |
| Create payment links / buttons / requests to send to customers | eCommerce |
| Initiate a payment on a physical terminal via API (no app on terminal) | ePOS |
| Trigger a refund on a physical terminal via API | ePOS |

**ePOS API vs Terminal SDKs:**
The ePOS API triggers a payment on a registered terminal from your backend — no app needs to be installed on the terminal or the controlling device. The Terminal SDKs (Android/iOS/.NET) require a controlling app to be running. Use ePOS API when you want backend-initiated terminal payments without deploying any terminal-side software.
