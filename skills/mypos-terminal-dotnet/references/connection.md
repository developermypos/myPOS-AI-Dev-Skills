> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## USB / Serial Port Connection

```csharp
myPOSTerminal terminal = new myPOSTerminal();
terminal.ProcessingFinished += Terminal_ProcessingFinished;

// Connect via COM port
terminal.Initialize("COM3");

// Optional: set COM timeout (milliseconds, default 30000)
terminal.SetCOMTimeout(30000);
```

Find the COM port number in Windows Device Manager under *Ports (COM & LPT)*.

## TCP-IP / WiFi Connection

```csharp
using System.Net.Sockets;

TcpClient tcp = new TcpClient();
tcp.Connect("192.168.1.100", 9100);  // terminal's local IP + port

myPOSTerminal terminal = new myPOSTerminal();
terminal.ProcessingFinished += Terminal_ProcessingFinished;
terminal.Initialize(tcp.GetStream());
```

## Disconnect

```csharp
terminal.Disconnect();
tcp?.Close();  // also close TcpClient if used
```

Call `Disconnect()` before the application exits or when releasing the terminal.

## Reading Order

1. `references/setup.md` — subscribe events before connecting.
2. This file — connect.
3. `references/payment.md` — first payment.

## See Also

- references/setup.md
- references/payment.md
