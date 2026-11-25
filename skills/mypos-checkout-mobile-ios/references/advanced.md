> Docs: https://developers.mypos.com/in-app-payments/ios-payments (SDK overview — closest doc
> anchor; the operations below are confirmed present in the compiled `MPCheckout.xcframework`
> headers but are **not yet covered on the public iOS SDK docs site**. Treat the framework's own
> header comments as the authoritative signature source, and verify behavior in sandbox before
> relying on any of these in production.)

## Pre-Authorization

Hold funds now, capture or cancel later.

```ObjectiveC
MPPreAuthorizationViewController *controller = [[MPPreAuthorizationViewController alloc] initWithItemName:@"Hotel Booking"
                                                                                                     amount:@"200.00"
                                                                                                    orderId:[[NSUUID UUID] UUIDString]
                                                                                                   delegate:self];
[self presentViewController:controller animated:YES completion:nil];
```

```ObjectiveC
[MPCheckout completePreAuthorization:orderId forAmount:@"150.00" inCurrency:MPCurrencyEUR
                          completion:^(NSString *transactionRef) { }
                             failure:^(MPCheckoutError *error) { }];

[MPCheckout cancelPreAuthorization:orderId
                         completion:^(NSString *transactionRef) { }
                            failure:^(MPCheckoutError *error) { }];

[MPCheckout getPreAuthorizationStatus:orderId
                            completion:^(MPPreAuthorizationStatus status) { }
                               failure:^(MPCheckoutError *error) { }];
```

`MPPreAuthorizationStatus` values: `MPPreAuthorizationStatusPending`,
`MPPreAuthorizationStatusCaptured`, `MPPreAuthorizationStatusDeclined`,
`MPPreAuthorizationStatusReversed`, `MPPreAuthorizationStatusCancelled`.

## Authorization (Card-on-File, Capture Separately)

```ObjectiveC
[MPCheckout makeAuthorizationForItem:@"Subscription"
                           withAmount:@"9.99"
                              orderId:[[NSUUID UUID] UUIDString]
                            cardToken:_selectedCardToken
                           completion:^(NSString *transactionRef) { }
                              failure:^(MPCheckoutError *error) { }];

[MPCheckout captureAuthorization:orderId forAmount:@"9.99"
                       completion:^(NSString *transactionRef) { }
                          failure:^(MPCheckoutError *error) { }];

[MPCheckout cancelAuthorization:orderId
                      completion:^(NSString *transactionRef) { }
                         failure:^(MPCheckoutError *error) { }];

[MPCheckout listAuthorizationsWithCompletion:^(NSArray<MPAuthorization *> *authorizations) { }
                                      failure:^(MPCheckoutError *error) { }];
```

`MPAuthorization` fields: `date`, `amount`, `currency`, `orderId`, `trnReference`.

## Reversal

Same-transaction cancellation, distinct from a Refund.

```ObjectiveC
[MPCheckout reverseTransaction:transactionRef
                     completion:^(NSString *transactionRef) { }
                        failure:^(MPCheckoutError *error) { }];
```

## Reading Order

1. `references/purchase.md` and `references/store-card.md` — the documented, stable operations.
2. This file — advanced, header-verified operations.

## See Also

- references/refund.md
- references/store-card.md
