> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Add the DLL Reference

The SDK is **not on NuGet** — add `myPOSTerminal.dll` (v2.0.0.1) as a project reference:

1. Right-click project → **Add** → **Reference** → **Browse**
2. Select `myPOS-SDK/myPOSTerminal.dll`
3. Add `using myPOS;` to your file

**.NET Framework:** 4.8 (minimum 4.5.2)

## Instantiate and Subscribe Events

```csharp
using myPOS;

myPOSTerminal terminal = new myPOSTerminal();

// MUST subscribe before any transaction call
terminal.ProcessingFinished += Terminal_ProcessingFinished;

// Optional interactive callbacks
terminal.onPresentCard   += (s, e) => UpdateUI("Present card");
terminal.onPresentPin    += (s, e) => UpdateUI("Enter PIN");
terminal.Log             += (s, e) => Debug.WriteLine(e.Message);
```

## Handle Transaction Results

```csharp
private void Terminal_ProcessingFinished(object sender, TransactionResult e)
{
    if (e.Approved)
    {
        string masked = e.MaskedPAN;
        string approvalCode = e.ApprovalCode;
        string reference = e.TransactionReference;
    }
    else
    {
        // e.ResponseCode / e.ResponseText — decline reason
    }
}
```

## Reading Order

1. This file — SDK setup.
2. `references/connection.md` — connect to terminal.
3. `references/payment.md` — first payment.

## See Also

- references/connection.md
