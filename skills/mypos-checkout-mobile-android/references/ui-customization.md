> Docs: https://developers.mypos.com/in-app-payments/android-payments/ui-customizations

## Hide the Custom Name Field

Applies to `StoreCardActivity` and `PurchaseActivity`:

```java
intent.putExtra(MyPos.INTENT_EXTRA_WITHOUT_CUSTOM_NAME, true);
```

## Set a Custom Banner/Logo

Applies to `StoreCardActivity`, `UpdateCardActivity`, and `PurchaseActivity`:

```java
intent.putExtra(MyPos.INTENT_EXTRA_CUSTOM_LOGO_RESOURCE, R.drawable.merchant_banner);
```

## Configure Colors

```xml
<style name="AppTheme" parent="Theme.MyPOS">
    <item name="colorButtonNormal">#F68121</item>
    <item name="colorAccent">@android:color/white</item>
</style>
```

Individual color overrides (use `tools:override="true"`): `myposFocusedColor`,
`myposEditTextHintColor`, `myposEditTextColor`, `myposEdtTitleTextColor`, `myposUnderlineColor`,
`myposButtonColor`, `myposButtonTextColor`, `myposTitleTextColor`, `myposBackgroundColor`.

## Configure Displayed Text

```xml
<style name="AppTheme" parent="Theme.MyPOS">
    <item name="activityTitle">@string/enter_card_details</item>
    <item name="buttonLabel">@string/pay_now</item>
</style>
```

Use clear, action-based button labels so the customer always knows what tapping the button will do.

## Reading Order

1. `references/purchase.md` or `references/store-card.md` — the flow you're customizing.
2. This file — apply branding.

## See Also

- references/purchase.md
- references/store-card.md
