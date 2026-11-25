> Docs: https://developers.mypos.com/sdks/online-checkout-typescript-sdk
> Setup guide: https://developers.mypos.com/online-payments/initial-setup-and-testing

## Install the SDK

```bash
npm install mypos-online-checkout
```

## Get the Config Pack

The config pack is a base64-encoded string containing your merchant credentials. Obtain it from:
- Production: the myPOS merchant portal
- Sandbox/demo: https://merchant-demo.mypos.eu/en/login

Store it in an environment variable:

```bash
# .env
MYPOS_CONFIG_PACK=your-base64-config-pack
```

## Initialize the Client (Server-Side Only)

```ts
import { MyPOSClient } from 'mypos-online-checkout';

const mypos = new MyPOSClient({
  configPack: process.env.MYPOS_CONFIG_PACK!,
  sandbox: process.env.NODE_ENV !== 'production',
});
```

Initialize once at application startup. The config pack contains your private key — never expose it client-side.

## Checkout URL

The SDK exposes the hosted checkout URL:

```ts
const checkoutUrl = mypos.checkoutUrl; // URL where the browser will POST the payment form
```

## Reading Order

1. This file — SDK setup.
2. `references/purchase.md` — first payment flow to implement.
3. `references/webhooks.md` — IPN handling (required).
4. `references/testing.md` — validate with test cards.

## See Also

- references/purchase.md
- references/webhooks.md
- references/testing.md
