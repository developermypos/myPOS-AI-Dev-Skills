> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Card Payment

After connecting (`references/connection.md`):

```csharp
// Returns immediately with RequestResult; actual outcome arrives in ProcessingFinished
RequestResult result = terminal.Purchase(
    amount: 25.00m,
    currency: "EUR",
    tipAmount: 0m
);

if (result != RequestResult.Processing)
{
    // Terminal busy, invalid params, or not initialized — handle before waiting for event
    HandleError(result);
    return;
}
// Wait for ProcessingFinished event
```

## ProcessingFinished Handler

```csharp
private void Terminal_ProcessingFinished(object sender, TransactionResult e)
{
    // Run on UI thread if updating UI
    this.Invoke((Action)(() =>
    {
        if (e.Approved)
        {
            lblStatus.Text = "Approved";
            string masked = e.MaskedPAN;
            string approvalCode = e.ApprovalCode;
        }
        else
        {
            lblStatus.Text = $"Declined: {e.ResponseText}";
        }
    }));
}
```

## RequestResult Values

| Value | Meaning |
|-------|---------|
| `Processing` | Request accepted, wait for event |
| `Busy` | Previous transaction still running |
| `InvalidParams` | Bad parameters passed |
| `NotInitialized` | `Initialize()` not called |

## Reading Order

1. `references/connection.md` — connect to terminal first.
2. This file — payment.
3. `references/refund-reversal.md` — handle refunds.

## See Also

- references/refund-reversal.md
- references/preauthorization.md
