> Docs: https://developers.mypos.com/in-app-payments/ios-payments/payment

## Perform a Payment

```Swift
let controller = MPPaymentViewController(cartItems: selectedCartItems,
                                         orderId: UUID().uuidString,
                                         delegate: self)
self.present(controller, animated: true, completion: nil)
```

```ObjectiveC
MPPaymentViewController *controller = [[MPPaymentViewController alloc] initWithCartItems:_selectedCartItems
                                                                                 orderId:[[NSUUID UUID] UUIDString]
                                                                                delegate:self];
[self presentViewController:controller animated:YES completion:nil];
```

```Swift
func paymentDidComplete(withReference transactionRef: String) {
    // success
}

func paymentDidFail(withError error: MPCheckoutError) {
    // inspect error before treating this as a final failure
}
```

```ObjectiveC
- (void)paymentDidCompleteWithReference:(NSString *)transactionRef;
- (void)paymentDidFailWithError:(MPCheckoutError *)error;
```

## Payment with a Stored Card

`MPPaymentViewController` also accepts a saved card token, optionally forcing 3D Secure and passing
the cardholder's email (useful when 3DS challenge screens require it):

```ObjectiveC
MPPaymentViewController *controller = [[MPPaymentViewController alloc] initWithCartItems:_selectedCartItems
                                                                                 orderId:[[NSUUID UUID] UUIDString]
                                                                               cardToken:_selectedCardToken
                                                                                forceTDS:NO
                                                                                   email:_customerEmail
                                                                                delegate:self];
[self presentViewController:controller animated:YES completion:nil];
```

The same `paymentDidCompleteWithReference:` / `paymentDidFailWithError:` delegate methods are
called on completion.

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — purchase and stored-card payment.
3. `references/transaction-status.md` — confirm status if needed.

## See Also

- references/store-card.md
- references/refund.md
- references/transaction-status.md
