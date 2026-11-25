> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/sample-code/setup
> Identity API: https://developers.mypos.com/apis/identity

## SDK Initialization

Install the SDK:

```bash
npm install mypos-api-gateway
```

Initialize `MyPOSGateway` once at application startup (singleton):

```ts
import { MyPOSGateway } from 'mypos-api-gateway';

const gateway = new MyPOSGateway({
  clientId: process.env.MYPOS_CLIENT_ID!,
  clientSecret: process.env.MYPOS_CLIENT_SECRET!,
});
```

## Creating a Merchant Client

Each merchant gets its own `MerchantClient` from a session token:

```ts
// Obtain a session token for the merchant (from your auth flow or stored token)
const client = gateway.createClient(merchantSessionToken);

// client.banking, client.ecommerce, client.epos are now available
```

## OAuth2 Flow

The SDK handles OAuth2 automatically:
- `clientId` + `clientSecret` → OAuth token (valid ~1 hour)
- OAuth token + merchant credentials → Session token (valid ~5 minutes)
- Session tokens are refreshed automatically by the SDK

Never expose `clientId` or `clientSecret` in client-side code or version control.

## Reading Order

1. `references/partner-portal.md` — get credentials first.
2. This file — initialize the SDK.
3. Pick one: `references/banking-api.md`, `references/ecommerce-api.md`, or `references/epos-api.md`.

## See Also

- references/partner-portal.md
- references/error-handling.md
- https://developers.mypos.com/apis/identity
