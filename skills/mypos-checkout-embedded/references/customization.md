> Docs: https://developers.mypos.com/sdks/embedded-sdk/customization

## Pass Customization as the 4th Argument

```javascript
const customization = {
    formBackgroundColor: '#e5f2fe',
    labelFontSize: '13px',
    labelColor: '#007ff5',
    inputBorderRadius: '6px',
    payButtonBorderColor: '#007ff5',
    payButtonBackgroundColor: '#007ff5',
    payButtonColor: '#fff',
    payButtonFontSize: '15px',
};

MyPOSEmbedded.createPayment(
    'myPOSEmbeddedCheckout',
    paymentParams,
    callbackParams,
    customization   // ← 4th argument
);
```

## All Customization Options

| Parameter | Effect |
|-----------|--------|
| `formBackgroundColor` | Background color of the entire form |
| `labelFontSize` | Font size of field labels (e.g. `'13px'`) — range 12px–16px |
| `labelColor` | Color of field labels |
| `inputBorderRadius` | Border radius of input fields (e.g. `'6px'` or `'0px'`) |
| `payButtonBorderColor` | Pay button border color (default state) |
| `payButtonBackgroundColor` | Pay button background color (default state) |
| `payButtonBorderColorHover` | Pay button border color on hover |
| `payButtonBackgroundColorHover` | Pay button background on hover |
| `payButtonColor` | Pay button text color |
| `payButtonColorHover` | Pay button text color on hover |
| `payButtonFontSize` | Pay button font size (e.g. `'15px'`) |

## Dark Theme Example

```javascript
const customization = {
    formBackgroundColor: '#555d60',
    labelFontSize: '12px',
    labelColor: '#fff',
    inputBorderRadius: '0px',
    payButtonBorderColor: '#f8be37',
    payButtonBackgroundColor: '#f8be37',
    payButtonColor: '#fff',
    payButtonFontSize: '14px',
};
```

## Reading Order

1. `references/card-payment.md` — get basic payment working first.
2. This file — then add styling.

## See Also

- references/deferred-payment.md
