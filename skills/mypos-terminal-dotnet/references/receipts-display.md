> Docs: https://developers.mypos.com/sdks/dotnet-sdk

## Reprint Last Receipt

```csharp
terminal.ReprintReceipt();
// No event callback — fire and forget
```

## Print Custom Receipt

Use text delimiters to format the printout:

| Delimiter | Effect |
|-----------|--------|
| `\n` | New line |
| `\L` | Print logo |
| `\c` | Center text |
| `\l` | Left-align |
| `\r` | Right-align |
| `\W` | Double width |
| `\H` | Double height |
| `\\` | Literal backslash |

```csharp
string receipt = "\L\n" +                    // logo
                 "\cTHANK YOU\n" +           // centered
                 "\lOrder: #1001\n" +        // left
                 "\rTotal: €25.00\n" +       // right
                 "\cwww.example.com\n";

terminal.PrintExternal(receipt);
// Use PrintExternalUTF8() for non-ASCII characters
```

## Display Text on Screen (5 rows)

```csharp
terminal.DisplayTextOnScreen(
    row1: "Welcome",
    row2: "Please wait",
    row3: "",
    row4: "",
    row5: "",
    row1Alignment: DisplayAlignment.Center,
    row2Alignment: DisplayAlignment.Center
);

terminal.HideTextOnScreen();
```

## Show Interactive Buttons

```csharp
terminal.DisplayButtons(
    button1: "Yes",
    button2: "No",
    button3: "",
    button4: "",
    button5: ""
);
// Result arrives in ProcessingFinished
```

## Beep

```csharp
terminal.Beep(BeepTone.Single);
// BeepTone options: Single, Double, Triple
```

## Reading Order

1. `references/connection.md` — connect first.
2. This file — display and print.

## See Also

- references/terminal-management.md
