---
name: mypos-api-gateway-ts
description: Guide for building myPOS API Gateway integrations using the TypeScript SDK for server-side Node.js applications. Use when implementing Banking API (accounts, transactions, statements), Ecommerce API (payment links, buttons, payment requests), or ePOS API (POS payments, terminal management, refunds, reversals) with OAuth2 authentication.
license: Apache-2.0
---

# myPOS API Gateway — TypeScript SDK

- Docs root: https://developers.mypos.com/
- SDK overview: https://developers.mypos.com/sdks/api-gateway-typescript-sdk

Use this skill to build server-side myPOS integrations in Node.js or TypeScript.

## Quick Decision Tree

```
Which myPOS API do you need?
├─ Account balances, transaction history, statements → Banking API
│  └─ references/banking-api.md
├─ Payment links, payment buttons, payment requests → Ecommerce API
│  └─ references/ecommerce-api.md
├─ Initiate POS payments, manage terminals, refunds → ePOS API
│  └─ references/epos-api.md
└─ Multiple merchants sharing one set of credentials → Multi-merchant
   └─ references/multi-merchant.md

Not sure which API?
└─ Start with references/README.md
```

## Start Here

1. **Check prerequisites**: The developer must have a myPOS Partner Portal account and a registered integration before writing any code. See `references/partner-portal.md`.
2. **Set up authentication**: OAuth2 client credentials → session token. See `references/authentication.md`.
3. **Choose the API group**: Banking, Ecommerce, or ePOS. Open only the one relevant reference file.
4. **Handle errors**: The SDK never throws on API errors — always check `result.success`. See `references/error-handling.md`.
5. **Test with the Demo Partner Portal** before going live. See `references/testing.md`.

## Non-Negotiable Rules

- **OAuth credentials are server-side only.** Never expose `clientId` or `clientSecret` in client-side code.
- **Check `result.success` on every API call.** The SDK uses a result pattern — it does not throw on API errors.
- **Session tokens expire in ~5 minutes.** The SDK handles refresh automatically, but never store or cache session tokens long-term in your own code.
- **OAuth tokens expire in ~1 hour.** Use `MyPOSGateway` as a singleton (created once at application startup).
- **Never hardcode credentials.** Read them from environment variables or a secrets manager.
- **Use the Demo Partner Portal** (`https://demo-partners.mypos.com/en`) for all development and testing work.

## Implementation Workflow

1. Confirm Partner Portal registration and integration credentials are available.
2. Initialize `MyPOSGateway` once at application startup with `clientId` and `clientSecret`.
3. Create a `MerchantClient` from the gateway using the merchant's session token.
4. Call the appropriate API (Banking / Ecommerce / ePOS) through the client.
5. Check `result.success`; handle errors from `result.error`.
6. For multi-merchant setups, create one client per merchant session. See `references/multi-merchant.md`.

## Required Response Contract

Every implementation must:

1. State which API group is being used (Banking / Ecommerce / ePOS) and why.
2. Show `MyPOSGateway` initialized from environment variables, not hardcoded values.
3. Show `result.success` check with explicit error handling on the false branch.
4. Include sandbox/demo setup instructions before production credentials.
5. Reference the correct documentation URL for the endpoint being used.

## Validation Checklist

- `clientId` and `clientSecret` are read from environment variables, not hardcoded.
- `MyPOSGateway` is instantiated once (singleton), not per-request.
- Every API call checks `result.success` before using the data.
- Tested against the Demo Partner Portal before switching to production credentials.
- Errors surface a meaningful message to the calling layer rather than swallowing silently.

## References

Use `references/README.md` to navigate to the right reference file.
