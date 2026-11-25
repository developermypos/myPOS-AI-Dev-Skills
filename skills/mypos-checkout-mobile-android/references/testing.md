> Docs: https://developers.mypos.com/in-app-payments/android-payments/testing

## Sandbox Testing

Initialize with `isSandbox = true` and test every flow you use — purchase, refund,
pre-authorization, and card storage — with no real funds moved:

```java
boolean isSandbox = true;
myPos.init(sid, walletNumber, currency, clientPrivateKey, serverPublicKey, isSandbox);
```

Sandbox credentials (SID, Wallet Number, keys) are separate from production credentials — obtain
both from the myPOS merchant portal and do not mix them.

## Rules

- Always use a **unique Order ID / Reference ID** per transaction or card-storage call.
- A "by appointment" test service is available to validate your integration directly with myPOS —
  request it through your merchant portal contact.
- Test both success and decline cases (bad CVC, invalid/expired card, 3D Secure failure) before
  going live.

## Going Live

Production traffic requires myPOS's approval. Email **integrations@mypos.com** to request app
review before switching `isSandbox` to `false` and using production credentials.

## Reading Order

1. `references/setup.md` — initialize the SDK.
2. This file — test thoroughly in sandbox.
3. Request production approval before going live.

## See Also

- references/setup.md
