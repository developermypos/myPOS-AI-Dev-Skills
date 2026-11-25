# Frequently Asked Questions

Quick answers to common questions that span multiple integrations.

---

**Q: Where do I start? What's the first step for any myPOS integration?**

Register in the myPOS Partner Portal before writing any code. Demo: `https://demo-partners.mypos.com`. You'll receive the credentials (clientId/clientSecret, SID, WalletNumber, API keys, or IPP SSL certificate) that every integration requires. You cannot test anything without them.

---

**Q: What's the difference between the Terminal SDK and IPP?**

Terminal SDKs (Android, iOS, .NET) are wrappers around IPP. The SDK handles TCP framing, 2-byte length headers, SSL certificate management, stage polling, and platform-specific callbacks — you call `startPayment()` or similar and handle the result. IPP is the underlying binary wire protocol. Use a Terminal SDK when one exists for your platform. Use IPP directly only when no SDK exists for your platform (e.g., embedded Linux, custom hardware), for kiosk/vending scenarios, or when you need direct protocol control.

---

**Q: Do I need OAuth?**

Only for API Gateway integrations (`mypos-api-gateway-ts` and `mypos-api-gateway-rest`). Checkout integrations use RSA-SHA256 request signing — no login, no tokens. Terminal SDKs and IPP use terminal activation — no per-request auth.

---

**Q: Can I test without a real myPOS terminal?**

- **Checkout (online payments):** Yes — use sandbox credentials and official test cards. No hardware needed.
- **API Gateway:** Yes — use demo credentials against `demo-api-gateway.mypos.com`. No hardware needed.
- **Terminal SDKs / IPP:** No — you need a Demo Debug POS device (test device provided by myPOS sales). Standard production terminals cannot run unsigned/development apps.
- **Glass SoftPOS:** You need an NFC-capable Android phone with the myPOS Glass app installed in test mode.

---

**Q: What's the difference between mypos-checkout-ts and mypos-checkout-rest?**

The same Checkout API, different ergonomics. `mypos-checkout-ts` is a TypeScript/Node.js SDK — it handles RSA-SHA256 signature computation, parameter serialization, and webhook validation for you. `mypos-checkout-rest` shows how to call the same API directly from any language, implementing the RSA signing yourself. If you're on Node.js and want less boilerplate, use the TS SDK. If you're on another language or want full HTTP control, use the REST approach.

---

**Q: What's the difference between mypos-smart-android and mypos-terminal-android?**

**Smart SDK:** Your Android app runs _on_ the myPOS Smart terminal device itself. The app and the card reader are the same physical device. Requires AppMarket distribution via Partner Portal.

**Terminal Android SDK:** Your Android app runs on a _separate_ device (phone, tablet) and controls a myPOS terminal connected via Bluetooth, USB, or TCP/IP. Two separate physical devices.

---

**Q: Can I accept payments in any currency?**

The currency you pass in the request (CURRENCY in IPP, `currency` in Checkout) must be supported by the terminal's or merchant account's configuration. Passing a currency code the terminal isn't configured for will result in a declined transaction or error. Contact myPOS to configure supported currencies for your merchant account.

---

**Q: What's the difference between PURCHASE and SALE?**

The same operation. "SALE" is the industry standard term for an immediate card charge. myPOS IPP and most myPOS SDKs call it `PURCHASE` / `METHOD=PURCHASE`. They refer to the same transaction type.

---

**Q: I got a STATUS=25 / STATUS=26 / STATUS=3 error — what do I do?**

These are IPP status codes:
- `STATUS=25` — terminal not activated → call `ACTIVATE` immediately, then `UPDATE`.
- `STATUS=26` — software update required → call `UPDATE`.
- `STATUS=3` — syntax error (wrong parameter) → check PROTOCOL is first, METHOD is second, DATA is last; check all property names are ALL CAPS.

Full status code table: `@skills/mypos-ipp/references/appendices.md` (Appendix I).

---

**Q: I got APPROVAL=05 / APPROVAL=51 / APPROVAL=91 — what does it mean?**

These are issuer response codes returned in the `APPROVAL` field of IPP STAGE=5 responses:
- `05` — Do not honor (issuer declined) → cardholder contacts bank.
- `51` — Insufficient funds → cardholder contacts bank.
- `91` — Issuer or switch inoperative (issuer system down) → retry later.

Full APPROVAL codes table: `@skills/mypos-ipp/references/debugging.md` (APPROVAL Codes section).

---

**Q: Which checkout method supports Apple Pay and Google Pay?**

Only `mypos-checkout-embedded`. The iFrame form renders Apple Pay and Google Pay buttons directly in your page. Standard redirect checkout (TS, PHP, REST) does not support Apple/Google Pay. Apple Pay additionally requires domain verification: host a file at `/.well-known/apple-developer-merchantid-domain-association` and register the domain in your myPOS account.

---

**Q: What's the difference between mypos-checkout-embedded and the Mobile Checkout SDKs (mypos-checkout-mobile-android/ios)?**

`mypos-checkout-embedded` renders an iFrame *inside a web page* — it runs in a browser or WebView context and supports Apple Pay/Google Pay wallet buttons. `mypos-checkout-mobile-android`/`mypos-checkout-mobile-ios` render *native* Activities (Android) or view controllers (iOS) directly inside a native mobile app — no WebView, no iFrame, and no wallet-button support. If you're building a website, use Embedded. If you're building a native Android or iOS app and want the card form to feel like part of the app itself, use the matching Mobile Checkout SDK.

---

**Q: My app needs to run on the terminal AND call the backend API — which skill do I use?**

Both. Load `mypos-smart-android` for the on-device payment and receipt logic. Load `mypos-api-gateway-ts` or `mypos-api-gateway-rest` for backend API calls (balances, payment links, transaction history). The two skills are independent and can be used together in the same project.

---

**Q: What's the difference between the eCommerce API and the Checkout API?**

- **eCommerce API** (part of `mypos-api-gateway-*`): Server-side API for creating payment links, payment buttons, and payment requests that are sent to customers. The customer receives a link and pays; your backend tracks the result via webhook.
- **Checkout API** (`mypos-checkout-*`): Web payment integration where your site redirects the customer to a myPOS checkout page, or embeds a myPOS payment form. The customer interacts with the checkout on your site or on the myPOS page.

---

**Q: When do I need the Partner Portal?**

Always — before writing any code. The Partner Portal is where you create the integration record that generates your credentials. Without it you have no `clientId`, `clientSecret`, SID, WalletNumber, or API keys to put into your code. There is no way to make an authenticated API call or connect to a terminal without first registering in the Partner Portal.

---

**Q: Can I use the Terminal SDK and the API Gateway in the same app?**

Yes. They are independent integrations. A common pattern: use `mypos-smart-android` or `mypos-terminal-android` to accept payments on a terminal, and `mypos-api-gateway-ts/rest` on your backend to read transaction history, generate payment links, or manage banking operations. They use different credentials and different APIs.

---

**Q: I need to refund a transaction — which skill and method?**

| Integration | How to refund |
|-------------|-------------|
| mypos-ipp | `METHOD=REFUND` (requires PASSWORD); see `@skills/mypos-ipp/references/refund.md` |
| mypos-terminal-android | `POSHandler.getInstance().startRefund(...)` |
| mypos-terminal-ios | `requestRefund(...)` |
| mypos-terminal-dotnet | `terminal.Refund(...)` after `SetPassword(...)` |
| mypos-smart-android | See `references/refunds-voids.md` in that skill |
| mypos-checkout-rest | `IPCRefund` — see `references/refund-reversal.md` in that skill |
| mypos-api-gateway-* | ePOS API refund endpoint |
| mypos-checkout-mobile-android | `RefundActivity` — see `references/refund.md` in that skill |
| mypos-checkout-mobile-ios | `MPCheckout.refundTransaction(...)` — see `references/refund.md` in that skill |
