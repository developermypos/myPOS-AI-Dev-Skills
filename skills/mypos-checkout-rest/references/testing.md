> Test cards: https://developers.mypos.com/online-payments/initial-setup-and-testing/testing-in-sandbox/test-cards
> Demo merchant portal: https://merchant-demo.mypos.eu/en/login

## Sandbox Setup

Sandbox and production use **different base URLs**:

| Environment | Redirect / form POST | Server-to-server POST |
|-------------|---------------------|----------------------|
| **Sandbox** | `https://www.mypos.com/vmp/checkout-test` | `https://www.mypos.com/vmp/checkout-api` (same) |
| **Production** | `https://www.mypos.com/vmp/checkout` | `https://www.mypos.com/vmp/checkout-api` (same) |

Use sandbox credentials together with the sandbox URL. Never mix sandbox credentials with production URLs or vice versa.

```python
CHECKOUT_URL = os.environ.get("MYPOS_SANDBOX") and \
               "https://www.mypos.com/vmp/checkout-test" or \
               "https://www.mypos.com/vmp/checkout"

MYPOS_SID         = os.environ["MYPOS_SID"]
MYPOS_WALLET      = os.environ["MYPOS_WALLET"]
MYPOS_KEY_INDEX   = os.environ["MYPOS_KEY_INDEX"]
MYPOS_PRIVATE_KEY = open(os.environ["MYPOS_PRIVATE_KEY_PATH"], "rb").read()
```

## Test Cards

The sandbox **rejects real card numbers** — only use official test cards. Example test cards from myPOS documentation:

| Card Number | Type | 3DS Behavior |
|-------------|------|-------------|
| `4111111111111111` | Visa | 3DS authenticated |
| `5454545454545454` | Mastercard | 3DS authenticated |
| `4444333322221111` | Visa | 3DS challenge required |

Use any future expiry date and any 3-digit CVC. Always check the official test card list at the link above for the current set.

## Demo Merchant Account

Log in at https://merchant-demo.mypos.eu/en/login to verify that:
- Payments appear in the transaction log
- Webhooks are being sent (check delivery log)
- Refunds process correctly

## Going Live

1. Replace sandbox credentials with production credentials from https://merchant.mypos.eu
2. Ensure `URL_Notify` is accessible from the internet over HTTPS
3. Ensure the production integration has been activated by myPOS (payment processing is disabled by default)

## See Also

- references/setup.md
- references/webhooks.md
