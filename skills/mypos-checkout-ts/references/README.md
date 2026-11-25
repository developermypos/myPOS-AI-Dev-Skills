# myPOS Online Checkout — References Index

> Docs: https://developers.mypos.com/sdks/online-checkout-typescript-sdk
> Checkout API: https://developers.mypos.com/apis/checkout-api

## Reading Order

1. `references/setup.md` — **Start here.** Config pack, SDK initialization, project setup.
2. Open exactly one payment flow reference based on what you need.
3. `references/webhooks.md` — always implement IPN handling.
4. `references/testing.md` — test with sandbox test cards before going live.

## Payment Flow References

| File | Use when... |
|------|-------------|
| `setup.md` | Setting up the SDK for the first time |
| `purchase.md` | Standard one-time card payment |
| `store-card.md` | Saving a card for future charges |
| `charge-stored-card.md` | Charging a previously saved card |
| `refund-reversal.md` | Refunding or reversing a completed payment |
| `preauthorization.md` | Holding funds; complete or cancel later |
| `stored-card-auth.md` | Authorizing a stored card; capture or reverse |
| `transfer-of-funds.md` | Sending or requesting money; mandate management |
| `webhooks.md` | Receiving and validating IPN notifications |
| `testing.md` | Sandbox test cards, demo environment |

## See Also

- https://developers.mypos.com/online-payments/initial-setup-and-testing
- https://developers.mypos.com/guides/integration-kits/online-payment-integration
