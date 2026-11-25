> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Mail / Phone Order (MoTo) Payment

```csharp
RequestResult result = terminal.MoToPurchase(
    amount: 50.00m,
    currency: "EUR"
);
// Customer reads card details over phone; cashier enters on terminal
```

MoTo variants: `MoToRefund()`, `MoToPreauthorization()`

## TWINT (Switzerland Only)

```csharp
RequestResult result = terminal.TwintPurchase(
    amount: 25.00m,
    currency: "CHF"   // CHF required
);
```

## Original Credit (Direct Credit to Card)

```csharp
terminal.SetPassword("manager-pin");
RequestResult result = terminal.OriginalCredit(
    amount: 10.00m,
    currency: "EUR"
);
```

## Cash Advance

```csharp
terminal.SetPassword("manager-pin");
RequestResult result = terminal.CashAdvance(
    amount: 100.00m,
    currency: "EUR"
);
```

## Send Payment Request (SMS / Email)

```csharp
RequestResult result = terminal.SendPaymentRequest(
    amount: 75.00m,
    currency: "EUR",
    recipient: "+359888123456",        // phone or email
    requestType: PaymentRequestType.SMS
);
```

## Vending / Unattended Payment Lifecycle

```csharp
// Start
RequestResult result = terminal.VendingPurchase(amount: 2.50m, currency: "EUR");

// After card tap — dispense product
terminal.VendingComplete();

// On dispense failure — release hold
terminal.VendingCancel();

// Abort before card presented
terminal.VendingStop();
```

## Reading Order

1. `references/payment.md` — understand the event-driven pattern first.
2. This file — advanced payment types.

## See Also

- references/payment.md
