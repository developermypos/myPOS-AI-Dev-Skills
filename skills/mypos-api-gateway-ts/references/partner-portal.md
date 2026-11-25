> Docs: https://developers.mypos.com/guides/onboarding-in-partners-portal
> Create integration: https://developers.mypos.com/guides/mypos-partner-create-integration

## Partner Portal Registration (Required Before Writing Any Code)

Before using the API Gateway, the developer must:

1. Register at the myPOS Partner Portal: https://partners.mypos.com/en
2. Create an integration inside the portal to receive OAuth2 credentials (`clientId` and `clientSecret`)
3. For development and testing, use the Demo Partner Portal instead: https://demo-partners.mypos.com/en

The Partner Portal is where credentials are managed, integrations are configured, and merchant access is granted.

## Demo Partner Portal (Development)

Use https://demo-partners.mypos.com/en for all development work:
- Create demo integrations without touching production systems
- Get sandbox `clientId` / `clientSecret` for testing
- Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## Reading Order

1. This file — confirm credentials are in hand before writing code.
2. `references/authentication.md` — use the credentials to initialize the SDK.

## See Also

- references/authentication.md
- references/testing.md
