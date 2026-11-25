> Docs: https://developers.mypos.com/sdks/dotnet-sdk
> Troubleshooting: https://developers.mypos.com/guides/troubleshooting-guides/terminal-sdk-and-api-integration

## Demo Debug POS Device (Required for Testing)

Production myPOS terminals require a live merchant account. For development testing:

- **Request a Demo Debug POS device** from the myPOS sales team
- The device ships with a **myPOS Demo Card** for simulating card payments
- Demo Merchant Account: https://merchant-demo.mypos.eu/en/login

## COM Port Discovery

```csharp
// List available COM ports
string[] ports = System.IO.Ports.SerialPort.GetPortNames();
// e.g. ["COM3", "COM5"]
```

Check Windows Device Manager under *Ports (COM & LPT)* to confirm which port is the terminal.

## Test Scenarios with Demo Card

- Successful payment
- Refund (requires password)
- Reversal
- Pre-authorization + completion
- Gift card activation

## Going Live

1. Replace the Demo Debug POS device with a production terminal
2. Register with a live merchant account
3. Use production credentials from the Partner Portal: https://partners.mypos.com/en

## Reading Order

1. `references/setup.md` — SDK setup.
2. `references/connection.md` — terminal connection.
3. This file — test setup.
4. `references/payment.md` — run a test payment.

## See Also

- references/connection.md
- references/payment.md
