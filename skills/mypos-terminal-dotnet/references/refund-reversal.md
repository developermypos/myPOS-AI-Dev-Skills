> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Refund

`SetPassword()` must be called before `Refund()`.

```csharp
terminal.SetPassword("manager-pin");

RequestResult result = terminal.Refund(
    amount: 25.00m,
    currency: "EUR",
    transactionReference: originalReference  // from the original ProcessingFinished event
);

if (result == RequestResult.Processing)
{
    // Wait for ProcessingFinished event — check e.Approved
}
```

## Reversal (Same-Day Void)

```csharp
terminal.SetPassword("manager-pin");

RequestResult result = terminal.Reversal(originalReference);
```

A reversal cancels the last transaction on the terminal. Only available on the same settlement day.

## Reading Order

1. `references/payment.md` — the original payment.
2. This file — refund or reverse it.

## See Also

- references/payment.md
- references/preauthorization.md
