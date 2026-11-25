> Docs: https://developers.mypos.com/sdks/embedded-sdk

## Option A — npm

```bash
npm i mypos-embedded-checkout
```

```javascript
import * as MyPOSEmbedded from 'mypos-embedded-checkout';
```

## Option B — CDN Script Tag

```html
<script src="https://developers.mypos.com/repository/mypos-embedded-sdk.js"
        type="text/javascript"></script>
<!-- SDK available as window.MyPOSEmbedded -->
```

## Add the Container Div

Place this where you want the payment form to appear:

```html
<!-- Minimum size: 320px wide × 620px tall -->
<div id="myPOSEmbeddedCheckout" style="min-width:320px; min-height:620px;"></div>
```

The iFrame is injected into this div. Smaller containers will clip the form.

## Credentials

You need two values from the myPOS merchant portal:
- **SID** (Store ID) — your merchant store reference number
- **Wallet Number** — your myPOS client/account number

Both are visible in your merchant portal account settings.

## Reading Order

1. This file — SDK setup.
2. `references/card-payment.md` — first payment.

## See Also

- references/card-payment.md
