> Docs: https://developers.mypos.com/in-app-payments/ios-payments/check-transaction-status

## Check Transaction Status

Use this to confirm a payment succeeded, verify a refund went through, or resolve an unknown result
state — never rely on on-device UI state alone for high-value order fulfillment.

```Swift
MPCheckout.getOrderStatus(orderId,
                          transactionType: type,
                          completed: { (status, reference) in
                              // authoritative status + reference
                          },
                          failed: { error in
                              // inspect error
                          })
```

```ObjectiveC
[MPCheckout getOrderStatus:_orderId
           transactionType:_transactionType
                completion:^(NSInteger status, NSString * _Nonnull paymentRef) {
                    // authoritative status + reference
                } failure:^(MPCheckoutError * _Nonnull error) {
                    // inspect error
                }];
```

`transactionType` is `.purchase` or `.refund` (Swift) / `MPTransactionTypePurchase` or
`MPTransactionTypeRefund` (Objective-C).

## Reading Order

1. `references/purchase.md` or `references/refund.md` — the transaction being checked.
2. This file — confirm its status.

## See Also

- references/purchase.md
- references/refund.md
