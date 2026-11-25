---
name: mypos-api-gateway-rest
description: Guide for calling the myPOS API Gateway directly over HTTP — no SDK required. Use when integrating ePOS terminal payments, payment links, payment buttons, payment requests, or banking/account data in any language or stack. Covers OAuth2 two-step authentication, ePOS payment creation, terminal management, eCommerce payment collection tools, and banking account and transaction access.
license: Apache-2.0
---

# myPOS API Gateway — Direct HTTP Integration

- Docs: https://developers.mypos.com/apis
- Identity (auth): https://developers.mypos.com/apis/identity
- ePOS API: https://developers.mypos.com/apis/epos
- eCommerce API: https://developers.mypos.com/apis/ecommerce
- Banking API: https://developers.mypos.com/apis/banking

Use this skill to call the myPOS API Gateway directly from any language or stack. The same 2-step OAuth2 authentication applies to all four sub-APIs.

## Quick Decision Tree

```
Authentication first — always required:
└── references/authentication.md
    (OAuth2 Bearer token → merchant session token → 4-header set)

ePOS — drive a physical payment terminal remotely:
├── Create payment on terminal, get status, reverse, refund
│   └── references/epos-payments.md
└── List terminals, activate/deactivate, get receipt
    └── references/epos-terminals.md

eCommerce — collect payments online without a checkout page:
├── Payment links (share via SMS, email, social media)
├── Payment buttons (embed on website)
└── Payment requests (send directly to customer for invoicing)
    └── references/ecommerce.md

Banking — read financial data:
├── List accounts (IBAN, balance, currency)
├── Transaction history (paginated)
├── Single transaction lookup
└── Download account statements (MT940, SWIFT)
    └── references/banking.md

First time? Start with:
└── references/setup.md → references/authentication.md
```

## Start Here

1. **Register**: Create an integration in the myPOS Partner Portal. You'll receive partner-level credentials (for OAuth) and merchant-level credentials (for session). See `references/setup.md`.
2. **Authenticate**: Every API call requires two tokens obtained in sequence. Read `references/authentication.md` before writing any request code.
3. **Pick your sub-API**: ePOS, eCommerce, or Banking — open the matching reference file.
4. **Use the demo environment**: All development goes to `https://demo-api-gateway.mypos.com`. See `references/testing.md`.

## Non-Negotiable Rules

- **Never hardcode `client_id` or `client_secret`** — read from environment variables or a secrets manager.
- **Session token expires in 5 minutes.** On a `401` response, re-create the session (POST `/api/v1/auth/session` again with the same Bearer token). Do not restart the full OAuth flow.
- **Bearer token expires in 1 hour.** On `401` after a session refresh, redo the OAuth token request.
- **All 4 headers are required on every API request**: `Authorization`, `X-Session`, `X-Partner-Id`, `X-Application-Id`.
- **Content-Type must be `application/json; x-api-version=1`** on all non-authentication requests.
- **Use the demo environment** (`https://demo-api-gateway.mypos.com`) during all development and testing.
- **Check HTTP status codes**: `2xx` = success, `4xx` = client error (check `errors` field), `5xx` = server error (retry).

## Implementation Workflow

1. Load partner credentials from environment variables.
2. POST to `/api/v1/oauth/token` to get a Bearer token (valid 1 hour). Cache it.
3. POST to `/api/v1/auth/session` with the Bearer token to get a session token (valid 5 min). Refresh on 401.
4. Build request headers: `Authorization`, `X-Session`, `X-Partner-Id`, `X-Application-Id`.
5. Make the API call with `Content-Type: application/json; x-api-version=1`.
6. On `401`: re-create session (step 3). If that also fails, redo OAuth (step 2).
7. Handle paginated list responses: `page`, `page_size`, `total_pages`, `has_next_page`.

## Required Response Contract

When helping implement this API, always:
- Show the full 2-step authentication flow (OAuth → session) in `references/authentication.md` first
- Show the 4 required headers on every API call
- Show `401` session refresh handling
- State which sub-API and endpoint is being used
- Show pagination handling for list endpoints
- Reference the demo environment for testing

## Validation Checklist

- [ ] Partner credentials in environment variables, not hardcoded
- [ ] OAuth token cached and reused for up to 1 hour
- [ ] Session token refreshed on 401 (not by redoing OAuth)
- [ ] All 4 required headers present on every request
- [ ] Content-Type is `application/json; x-api-version=1`
- [ ] Demo environment used during development
- [ ] Paginated responses handled (`has_next_page`)
- [ ] Error responses (`4xx`) inspected for the `errors` field

## References

See `references/README.md` for the full index and reading order.
