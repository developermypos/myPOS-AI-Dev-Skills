> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/sample-code/setup

## Multi-Merchant Pattern

One `MyPOSGateway` instance (one set of OAuth credentials) can serve multiple merchants. Each merchant gets its own `MerchantClient` with its own session token.

```ts
import { MyPOSGateway } from 'mypos-api-gateway';

// One gateway — created once at app startup
const gateway = new MyPOSGateway({
  clientId: process.env.MYPOS_CLIENT_ID!,
  clientSecret: process.env.MYPOS_CLIENT_SECRET!,
});

// Per-request: create a client for the specific merchant
async function handleRequest(merchantSessionToken: string) {
  const client = gateway.createClient(merchantSessionToken);

  const result = await client.banking.getAccounts();
  if (result.success) {
    return result.data.accounts;
  }
  throw new Error(result.error.message);
}
```

## Session Token Management

- Session tokens come from your authentication system (merchant logs in, you obtain their session token).
- Each session token is scoped to one merchant.
- The SDK refreshes expired session tokens automatically.
- Do not store session tokens long-term — let the SDK manage their lifecycle.

## Reading Order

1. `references/authentication.md` — understand the gateway and client pattern.
2. This file — multi-merchant setup.
3. Pick an API reference: `banking-api.md`, `ecommerce-api.md`, or `epos-api.md`.

## See Also

- references/authentication.md
- references/error-handling.md
