> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Activate a Terminal

```csharp
RequestResult result = terminal.Activate();
// ProcessingFinished event fires on completion
```

First-time activation registers the terminal with a merchant account.

## Deactivate a Terminal

```csharp
RequestResult result = terminal.Deactivate();
```

## Update Terminal Software

```csharp
RequestResult result = terminal.Update();
// Terminal downloads and applies pending updates; may reboot
```

## Get Terminal Status

```csharp
RequestResult result = terminal.GetStatus();
// Status details returned in ProcessingFinished event
```

## Ping

```csharp
RequestResult result = terminal.Ping();
// Confirms the terminal connection is alive
```

## Reboot Terminal

```csharp
terminal.Reboot();
// No event callback
```

## Open Terminal Settings

```csharp
terminal.OpenSettings();
// Opens the terminal's built-in settings menu
```

## Reading Order

1. `references/connection.md` — connect first.
2. This file — terminal lifecycle.

## See Also

- references/connection.md
