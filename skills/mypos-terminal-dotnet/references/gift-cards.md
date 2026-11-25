> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Activate a Gift Card

```csharp
RequestResult result = terminal.GiftcardActivation(
    amount: 50.00m,
    currency: "EUR"
);
// Wait for ProcessingFinished — e.Approved = true if activated
```

## Check Gift Card Balance

```csharp
RequestResult result = terminal.GiftcardCheckBalance();
// Balance returned in ProcessingFinished event data
```

## Deactivate a Gift Card

```csharp
RequestResult result = terminal.GiftcardDeactivation();
```

All three methods follow the same event-driven pattern — subscribe to `ProcessingFinished` before calling.

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. `references/payment.md` — understand the event pattern.
3. This file — gift card operations.

## See Also

- references/payment.md
