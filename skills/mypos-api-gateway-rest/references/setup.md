> Docs: https://developers.mypos.com/apis/identity
> Partner Portal: https://partners.mypos.com/en
> Demo Partner Portal: https://demo-partners.mypos.com/en

## Prerequisites

Before making any API calls you need:
1. A myPOS Partner Portal account at https://partners.mypos.com/en
2. A registered integration, which gives you two credential pairs:

**Partner-level credentials** — used for OAuth2 (step 1 of authentication):
- `client_id` — format: `client_XXXXXXXXX`
- `client_secret` — format: `secret_XXXXXXXXX`

**Merchant-level credentials** — used for session creation (step 2 of authentication):
- `client_id` — format: `cli_XXXXXXXXX`
- `client_secret` — format: `sec_XXXXXXXXX`

These are different credential pairs used at different steps. Store both securely in environment variables.

```bash
# .env (server-side only, never committed)
MYPOS_PARTNER_CLIENT_ID=client_XXXXXXXXX
MYPOS_PARTNER_CLIENT_SECRET=secret_XXXXXXXXX
MYPOS_MERCHANT_CLIENT_ID=cli_XXXXXXXXX
MYPOS_MERCHANT_CLIENT_SECRET=sec_XXXXXXXXX
MYPOS_PARTNER_ID=mps-p-XXXXXXXX
MYPOS_APPLICATION_ID=mps-app-XXXXXXXX
```

## Base URLs

| Environment | Base URL |
|-------------|----------|
| Demo | `https://demo-api-gateway.mypos.com` |
| Production | `https://api-gateway.mypos.com` |

Use the demo environment during all development. Never mix demo and production credentials.

## Required Headers for All API Requests

Once authenticated, every request to ePOS, eCommerce, or Banking endpoints must include:

| Header | Value |
|--------|-------|
| `Authorization` | `Bearer <oauth_bearer_token>` |
| `X-Session` | `<merchant_session_id>` |
| `X-Partner-Id` | `mps-p-XXXXXXXX` |
| `X-Application-Id` | `mps-app-XXXXXXXX` |
| `Content-Type` | `application/json; x-api-version=1` |

## Reading Order

1. This file — credentials and base URLs
2. `references/authentication.md` — obtaining the tokens and building the headers

## See Also

- references/authentication.md
- references/testing.md
