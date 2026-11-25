# Cross-Skill Concepts

Topics that apply across multiple myPOS integrations and don't belong to any single skill.

---

## Demo vs Production Environments

**Always develop and test in the demo/sandbox environment first.** Never use production credentials during development.

| Integration group | Demo environment | Production environment |
|-------------------|-----------------|----------------------|
| API Gateway | `demo-api-gateway.mypos.com` | `api-gateway.mypos.com` |
| Checkout SDK / REST | Sandbox flag in config (`isSandbox: true`) or demo endpoint URL | Production flag / production endpoint |
| Checkout Embedded | `callbackParams.isSandbox: true` | `callbackParams.isSandbox: false` |
| Terminal SDKs (Android/iOS/.NET) | Demo Debug POS device (test device from myPOS sales) | Registered production terminal with active merchant account |
| IPP (direct protocol) | Demo Debug POS device (same as Terminal SDKs) | Registered production terminal |
| Glass SoftPOS | myPOS Glass app in test mode | myPOS Glass app in production mode |
| Mobile Checkout SDKs (Android/iOS) | `isSandbox: true` at init | `isSandbox: false` + approval from `integrations@mypos.com` |

**Demo credentials are separate from production credentials.** Credentials obtained from `demo-partners.mypos.com` only work against demo endpoints, and vice versa. Do not mix them.

**Test cards:** Checkout and API Gateway (Checkout flows) use test card numbers. Do not use real cards in sandbox. Official test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards

**Demo terminal device:** Terminal SDK and IPP integrations require a hardware device registered as a Demo Debug POS. Request one from myPOS sales. Standard production terminals cannot run development/unsigned apps.

---

## Partner Portal

The Partner Portal is the administrative interface where integrations are created, credentials are issued, and terminal apps are registered.

| Environment | URL |
|-------------|-----|
| Sandbox / demo | `https://demo-partners.mypos.com` |
| Production | `https://partners.mypos.com` |

**What you do in the Partner Portal:**
- Create an integration → receive `clientId` / `clientSecret` (API Gateway), or SID + WalletNumber + key files (Checkout)
- Register terminal apps (required for mypos-smart-android AppMarket distribution)
- Download SSL certificates (required for IPP Slave mode)
- Manage merchant accounts and permissions

**Which skills require it:** All of them. Every myPOS integration requires at least an integration record in the Partner Portal before any API call or terminal connection can work.

**First step for every project:** Register in the Partner Portal (demo first) before writing any code. The credentials you get there are the first arguments to every SDK initializer and the first fields in every IPP ACTIVATE request.

---

## Authentication Patterns

Authentication varies significantly across integration groups:

### API Gateway — OAuth2 Client Credentials
- **Who uses it:** `mypos-api-gateway-ts` and `mypos-api-gateway-rest`
- **Flow:** POST client credentials → Bearer token (valid 1 hour) → POST Bearer token → Session token (valid 5 minutes) → 4-header set on every API request
- **Required headers:** `Authorization: Bearer <token>`, `X-Session: <session>`, `X-Partner-Id`, `X-Application-Id`
- **On 401:** Re-create session token (don't redo full OAuth). If session refresh also fails, redo OAuth.
- **SDK:** `mypos-api-gateway-ts` handles token refresh automatically.

### Checkout — RSA/HMAC Request Signing
- **Who uses it:** `mypos-checkout-ts`, `mypos-checkout-php`, `mypos-checkout-rest`
- **Flow:** Every request is signed with the merchant's RSA private key (PKCS#1 SHA-256). No login, no session tokens.
- **Signature computation:** Sort all parameters by key name (case-sensitive) → concatenate values → SHA-256 hash → RSA sign → Base64 encode → add as `Signature` parameter.
- **SDK:** TS and PHP SDKs compute signatures automatically. Direct REST requires manual implementation.
- **Private key storage:** Must never be web-accessible. Store outside document root or in a secrets manager.

### Terminal SDKs / IPP — Terminal Activation
- **Who uses it:** All Terminal SDKs and mypos-ipp
- **Flow:** Terminal is activated once (ACTIVATE → UPDATE in IPP; equivalent activation step in SDKs). After activation the terminal holds its own credentials. No per-request auth tokens.
- **SID:** IPP uses a unique session ID (SID) per request — this is a session identifier, not an authentication token.

### Checkout Embedded — Session Token
- **Who uses it:** `mypos-checkout-embedded`
- **For Apple/Google Pay only:** Server calls `PaymentSessionCreate` API → gets session token → passes to client SDK → SDK uses it for the payment button.
- **Card form:** Standard card payments don't require a server-side session token.

### Mobile Checkout SDKs — Client-Side Key/Certificate or Config Pack
- **Who uses it:** `mypos-checkout-mobile-android`, `mypos-checkout-mobile-ios`
- **Flow:** Credentials (a single `configurationKey` from a config pack, or the Advanced Setup
  SID/Wallet Number/key-pair or account/store/certificate/private-key combination) are provided
  once at SDK initialization and embedded inside the compiled app itself. No OAuth, no session
  tokens, and no server-side RSA request signing by the merchant — the SDK signs its own requests
  internally using the credentials it was initialized with.
- **Implication:** Because credentials ship inside the app binary, treat them with the same care as
  any embedded secret — obtain sandbox and production values separately from the merchant portal
  and never commit production values to a public repository.

---

## Webhooks and IPN

Webhooks deliver asynchronous payment outcome notifications from myPOS to your server.

**Who uses them:** Checkout skills (all variants), API Gateway (eCommerce sub-API).

**Pattern:** myPOS POSTs to your `urlNotify` / `notificationUrl` endpoint after a payment event (success, failure, cancellation).

**Common rules across all webhook implementations:**
1. **Always verify the signature** before acting on any webhook. Never update order status based on an unverified callback — this is a security vulnerability.
2. **Respond HTTP 200** to acknowledge. Any other status code causes myPOS to retry.
3. **Do not rely on `urlOk` redirect** as payment confirmation — the customer can be redirected to `urlOk` without a completed payment (browser navigation, spoofing). Always use the webhook or `IPCGetPaymentStatus` for definitive confirmation.
4. **Idempotency:** Webhooks may be delivered more than once. Design your handler to be idempotent (duplicate delivery should not double-process an order).

**Terminal SDKs:** Do not use webhooks. Payment results arrive in `onActivityResult()` callbacks (Android SDK), completion blocks (iOS SDK), or `ProcessingFinished` events (.NET SDK).

**IPP:** No webhooks. Stage responses (STAGE=5) carry the final result. Call COMPLETE_TX after every financial transaction.

**Mobile Checkout SDKs (`mypos-checkout-mobile-android`/`-ios`):** No webhooks either. Results arrive
only via `onActivityResult()` (Android) or delegate/completion callbacks (iOS). For authoritative
confirmation before fulfilling a high-value order, call the SDK's own status check
(`IPCGetTransactionStatus` / `MPCheckout.getOrderStatus`) rather than trusting on-device state alone.

---

## Card Data Security

None of the myPOS integrations expose full card numbers to your application in normal operation. This is intentional and by design.

| Integration | What your code receives | What is never exposed |
|-------------|------------------------|----------------------|
| mypos-smart-android | Masked PAN (`**NNNN`), approval code, transaction reference | Full PAN, CVV, PIN |
| mypos-terminal-android | Masked PAN, approval code | Full PAN, CVV, PIN |
| mypos-terminal-ios | Masked PAN (`e.maskedPAN` in MPTransactionData) | Full PAN, CVV, PIN |
| mypos-terminal-dotnet | Masked PAN (`e.MaskedPAN`), approval code | Full PAN, CVV, PIN |
| mypos-ipp | Masked PAN in response fields, APPROVAL code | Full PAN, CVV, PIN |
| mypos-checkout-ts/php/rest (redirect) | Transaction reference, card token (optional), approval | Full PAN, CVV (form is hosted by myPOS) |
| mypos-checkout-rest (server-to-server) | Card token, approval | Raw PAN only if PCI SAQ-D certified (IPCIAPurchase with raw card) |
| mypos-checkout-embedded | Transaction reference, approval | Full PAN, CVV (form runs in myPOS iFrame) |
| mypos-checkout-mobile-android/ios | Transaction reference, card token (optional), approval | Full PAN, CVV (card entered inside the SDK's own Activity/view controller) |
| mypos-api-gateway | Transaction history, amounts, reference numbers | Card numbers |

**PCI scope note:** Redirect and embedded checkout approaches minimize PCI scope (SAQ A) because card data never touches your server. Server-to-server charges using raw card numbers (`IPCIAPurchase` with PAN + CVV) require PCI DSS SAQ D compliance — do not implement this path without verifying your PCI certification status.
