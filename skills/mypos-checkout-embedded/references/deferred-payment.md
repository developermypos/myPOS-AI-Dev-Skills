> Docs: https://developers.mypos.com/sdks/embedded-sdk/sample-code

## Deferred Payment with a Custom Pay Button

Use `createPaymentForm()` when you want to control when payment is submitted — for example, your own styled Pay button outside the iFrame.

```html
<!-- Container for the card form (no Pay button inside) -->
<div id="myPOSEmbeddedCheckout" style="min-width:320px; min-height:620px;"></div>

<!-- Your own Pay button, disabled until the form is ready -->
<button id="btn-pay" disabled>Pay €23.45</button>
```

```javascript
import * as MyPOSEmbedded from 'mypos-embedded-checkout';

// paymentParams and callbackParams are identical to createPayment()
MyPOSEmbedded.createPaymentForm(
    'myPOSEmbeddedCheckout',
    paymentParams,
    callbackParams
).then(function (payment) {
    // Form is ready — enable the button
    const btn = document.getElementById('btn-pay');
    btn.disabled = false;

    btn.addEventListener('click', function () {
        btn.disabled = true;  // prevent double-click
        payment.processPayment();
    });
});
```

`payment.processPayment()` submits the card data entered in the iFrame form.

## When to Use This vs createPayment()

| | `createPayment()` | `createPaymentForm()` |
|--|--|--|
| Pay button | Inside the iFrame | Your own element |
| Button styling | Limited (customization param) | Fully custom |
| Timing control | Auto | Explicit via `.processPayment()` |

## Reading Order

1. `references/setup.md` — setup first.
2. `references/card-payment.md` — understand `paymentParams` and `callbackParams`.
3. This file — custom button integration.

## See Also

- references/card-payment.md
- references/customization.md
