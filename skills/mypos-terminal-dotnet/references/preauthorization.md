> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Pre-Authorize (Hold Funds)

```csharp
RequestResult result = terminal.Preauthorization(
    amount: 100.00m,
    currency: "EUR"
);
// Wait for ProcessingFinished event — store e.TransactionReference for completion
```

## Complete Pre-Authorization (Capture)

```csharp
RequestResult result = terminal.CompletePreauth(
    amount: 85.00m,               // can be less than authorized amount
    currency: "EUR",
    transactionReference: preauthRef
);
```

## Cancel Pre-Authorization (Release Hold)

```csharp
RequestResult result = terminal.CancelPreauth(
    transactionReference: preauthRef
);
```

All three methods return `RequestResult` immediately; the outcome arrives in `ProcessingFinished`.

## Reading Order

1. `references/payment.md` — basic payment pattern and event handling.
2. This file — pre-authorization.

## See Also

- references/payment.md
