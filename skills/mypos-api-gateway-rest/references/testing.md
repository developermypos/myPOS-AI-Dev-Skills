> Demo Partner Portal: https://demo-partners.mypos.com/en
> Demo Merchant Portal: https://merchant-demo.mypos.eu/en/login

## Demo Environment

All development and testing uses the demo environment. There is no separate API version — the same endpoints exist at a different base URL:

```python
BASE_URL = "https://demo-api-gateway.mypos.com"   # development
# BASE_URL = "https://api-gateway.mypos.com"       # production — only after go-live
```

Use demo credentials from the demo Partner Portal. Never mix demo and production credentials.

## Setup

1. Register at https://demo-partners.mypos.com/en
2. Create a test integration to receive demo partner credentials and merchant credentials
3. Configure your `.env` with the demo values:

```bash
MYPOS_PARTNER_CLIENT_ID=client_DEMO_XXXXX
MYPOS_PARTNER_CLIENT_SECRET=secret_DEMO_XXXXX
MYPOS_MERCHANT_CLIENT_ID=cli_DEMO_XXXXX
MYPOS_MERCHANT_CLIENT_SECRET=sec_DEMO_XXXXX
MYPOS_PARTNER_ID=mps-p-XXXXXXXX
MYPOS_APPLICATION_ID=mps-app-XXXXXXXX
```

## Testing ePOS Payments

ePOS testing requires a Demo Debug POS device. Request one from the myPOS sales team. The demo device ships with a myPOS Demo Card for testing payment flows.

Log into the Demo Merchant Portal at https://merchant-demo.mypos.eu/en/login to verify:
- Transactions appear in the transaction log
- Terminal status is correct
- Receipts are generated

## Going Live

1. Register at https://partners.mypos.com/en (production)
2. Replace all environment variables with production credentials
3. Change `BASE_URL` to `https://api-gateway.mypos.com`
4. Ensure the integration has been reviewed and activated by myPOS

## See Also

- references/setup.md
- references/authentication.md
