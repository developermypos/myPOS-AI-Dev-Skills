> Docs: https://developers.mypos.com/in-app-payments/ios-payments/add-a-card

## Add a Card

```Swift
let controller = MPStoreCardViewController(verificationAmount: 1.00, delegate: self)
present(controller, animated: true, completion: nil)
```

```ObjectiveC
MPStoreCardViewController *controller = [[MPStoreCardViewController alloc] initWithVerificationAmount:1.00
                                                                                             delegate:self];
[self presentViewController:controller animated:YES completion:nil];
```

`MPStoreCardViewController` also supports an `autoReverse` flag (automatically refunds the
verification amount once the card is verified) and a `withoutCustomName` flag (hides the custom
name field):

```ObjectiveC
MPStoreCardViewController *controller = [[MPStoreCardViewController alloc] initWithVerificationAmount:1.00
                                                                                             autoReverse:YES
                                                                                                delegate:self];
```

Delegate methods:

```Swift
func storeCardDidComplete(withData storedCard: MPStoredCard)
func storeCardDidFail(withError error: MPCheckoutError)
```

```ObjectiveC
- (void)storeCardDidCompleteWithData:(MPStoredCard *)storedCard;
- (void)storeCardDidFailWithError:(MPCheckoutError *)error;
```

## Update a Stored Card

```Swift
let controller = MPUpdateStoredCardViewController(cardToken: selectedCard.token,
                                                  verificationAmount: 1.00,
                                                  delegate: self)
present(controller, animated: true, completion: nil)
```

```ObjectiveC
MPUpdateStoredCardViewController *controller = [[MPUpdateStoredCardViewController alloc] initWithCardToken:_card.token
                                                                                        verificationAmount:1.00
                                                                                                  delegate:self];
[self presentViewController:controller animated:YES completion:nil];
```

```Swift
func updateStoredCardDidComplete(withData storedCard: MPStoredCard, forCardWithToken cardToken: String)
func updateStoredCardDidFail(withError error: MPCheckoutError)
```

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — save or update a card.
3. `references/purchase.md` — charge the stored card.

## See Also

- references/purchase.md
- references/advanced.md
