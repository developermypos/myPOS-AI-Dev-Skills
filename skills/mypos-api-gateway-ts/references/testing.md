> Demo Partner Portal: https://demo-partners.mypos.com/en
> Onboarding guide: https://developers.mypos.com/guides/onboarding-in-partners-portal

## Testing Setup

### 1. Use the Demo Partner Portal

All development and testing must be done against the Demo environment:

- Demo Partner Portal: https://demo-partners.mypos.com/en
- Register here and create a demo integration to receive sandbox `clientId` and `clientSecret`

### 2. Demo Merchant Account

Create a demo merchant account for testing:

- https://merchant-demo.mypos.eu/en/login

### 3. Environment Variables

```bash
# .env (development — demo credentials)
MYPOS_CLIENT_ID=your-demo-client-id
MYPOS_CLIENT_SECRET=your-demo-client-secret
```

Never commit credentials to version control.

### 4. Initialize Against Demo Environment

```ts
const gateway = new MyPOSGateway({
  clientId: process.env.MYPOS_CLIENT_ID!,
  clientSecret: process.env.MYPOS_CLIENT_SECRET!,
  // SDK points to demo endpoints when initialized with demo credentials
});
```

### 5. Going Live

Switch to production credentials (from the production Partner Portal at https://partners.mypos.com/en) when ready. No code changes needed — just swap the environment variables.

## Reading Order

1. `references/partner-portal.md` — register and get credentials.
2. `references/authentication.md` — initialize the SDK.
3. This file — verify test setup is correct.

## See Also

- references/partner-portal.md
- references/authentication.md
