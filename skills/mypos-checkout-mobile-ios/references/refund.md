> Docs: https://developers.mypos.com/in-app-payments/ios-payments/payment (refund is documented on
> the same page as Purchase in the official iOS SDK docs — there is no separate refund page)

## Perform a Refund

Requires the transaction reference, order ID, and amount of the payment being refunded.

```Swift
MPCheckout.refundTransaction(transaction.reference,
                             orderId: transaction.orderId,
                             amount: amount,
                             completed: { transactionRef in
                                 // refund succeeded
                             },
                             failed: { error in
                                 // inspect error
                             })
```

```ObjectiveC
[MPCheckout refundTransaction:_transaction.reference
                      orderId:_transaction.orderId
                       amount:_amount
                    completed:^(NSString * _Nonnull transactionRef) {
                        // refund succeeded
                    } failed:^(MPCheckoutError * _Nonnull error) {
                        // inspect error
                    }];
```

Make sure you're using the correct transaction reference for the payment being refunded.

## Reading Order

1. `references/purchase.md` — the original payment.
2. This file — refund it.
3. `references/transaction-status.md` — confirm the refund status if needed.

## See Also

- references/transaction-status.md
