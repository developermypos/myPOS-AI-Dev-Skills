> Docs: https://developers.mypos.com/in-app-payments/ios-payments/test-mobile-sdk-ios-checkout
> Security & availability: https://developers.mypos.com/in-app-payments/ios-payments/security-availability

## Sandbox Testing

Initialize with `isSandbox: true` and don't move to the live environment until you're confident
everything works as expected in sandbox:

```Swift
MPCheckout.initialize(in: .EUR, withConfigurationKey: sandboxConfigurationKey, isSandbox: true)
```

Test scenarios to cover before going live:
- **Successful payments and refunds** — confirm the result and any redirect/dismissal behavior.
- **Declined payments** — invalid CVC, invalid or expired card number, insufficient funds.
- **3D Secure** — both approved and failed challenge outcomes.

A "by appointment" test service is available to validate your integration directly with myPOS —
request it through your merchant portal contact.

## Key Errors to Handle

`MPCheckoutError` includes (non-exhaustive): `MPCheckoutInsufficientFundsError`,
`MPCheckoutDeclinedByCardIssuerError`, `MPCheckoutOrderIdAlreadyUsedError`,
`MPCheckoutTimeoutError`, `MPCheckoutInvalidStoreIdError`, `MPCheckoutLimitExceededError`. Always
branch on the error rather than assuming any failure is the same.

## Going Live

Production traffic requires myPOS's approval. Email **integrations@mypos.com** to request app
review before switching `isSandbox` to `false` and using production credentials.

## Reading Order

1. `references/setup.md` — initialize the SDK.
2. This file — test thoroughly in sandbox.
3. Request production approval before going live.

## See Also

- references/setup.md
