> Docs: https://developers.mypos.com/in-app-payments/ios-payments/ui-customizations
> Built-in parameters: https://developers.mypos.com/in-app-payments/ios-payments/built-in-parameters

## Configure the Theme

```Swift
var theme                  = MPCheckoutTheme()
theme.labelFontSize        = 13.0
theme.textFieldFont        = UIFont.systemFont(ofSize: 14.0)
theme.labelTextColor       = UIColor.defaultTextColor
theme.placeholderColor     = UIColor.lightGray
theme.barButtonItemColor   = UIColor.buttonEnabled
theme.textFieldTextColor   = UIColor.defaultTextColor
theme.placeholderFontSize  = 13.0
theme.textFieldBorderColor = UIColor.gray.withAlphaComponent(0.3)
theme.navigationTitleColor = UIColor.defaultTextColor
theme.placeholderAlignment = .left
```

```ObjectiveC
MPCheckoutTheme *theme     = [MPCheckoutTheme new];
theme.labelFontSize        = 13.0f;
theme.textFieldFont        = [UIFont systemFontOfSize:14.0f];
theme.labelTextColor       = [UIColor defaultTextColor];
theme.placeholderColor     = [UIColor lightGrayColor];
theme.barButtonItemColor   = [UIColor buttonEnabled];
theme.textFieldTextColor   = [UIColor defaultTextColor];
theme.placeholderFontSize  = 13.0f;
theme.textFieldBorderColor = [[UIColor grayColor] colorWithAlphaComponent:.3];
theme.navigationTitleColor = [UIColor defaultTextColor];
theme.placeholderAlignment = NSTextAlignmentLeft;
```

Apply it once, before presenting any payment view controller:

```Swift
MPCheckout.applyTheme(theme)
```

```ObjectiveC
[MPCheckout applyTheme:theme];
```

Other built-in parameters the SDK manages internally: `Amount`, `Currency`, `OrderID`,
`CartItems`, `VerificationAmount`, `CardToken`, `CustomerEmail`, `CustomerPhone`,
`AddressVerification`, `TransactionReference`.

## Reading Order

1. `references/purchase.md` or `references/store-card.md` — the flow you're customizing.
2. This file — apply branding.

## See Also

- references/purchase.md
- references/store-card.md
