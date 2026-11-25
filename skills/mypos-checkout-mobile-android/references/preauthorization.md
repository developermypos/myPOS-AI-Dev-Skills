> Docs: https://developers.mypos.com/in-app-payments/android-payments/pre-authorization
> With stored card: https://developers.mypos.com/in-app-payments/android-payments/pre-authorization-with-stored-card
> Completion: https://developers.mypos.com/in-app-payments/android-payments/pre-authorization-completion
> Cancellation: https://developers.mypos.com/in-app-payments/android-payments/pre-authorization-cancellation
> Status: https://developers.mypos.com/in-app-payments/android-payments/get-pre-authorization-status

Pre-Authorization requires SDK `1.2.8`+ (`com.mypos:mobilepaymentssdk:1.2.8`, Maven Central). If
`PreAuthorizationActivity` or `IPCPreAuthStatus` don't resolve, confirm the dependency version.

## Hold Funds (Pre-Authorization)

```java
Intent intent = new Intent(this, PreAuthorizationActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_AMOUNT, 200.00f);
intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, UUID.randomUUID().toString());
startActivityForResult(intent, MyPos.REQUEST_CODE_PRE_AUTHORIZATION);
```

With a stored card, add `MyPos.INTENT_EXTRA_CARD_TOKEN` to the same intent.

## Complete (Capture) or Cancel (Release)

Use the **same Order ID** as the original pre-authorization. The captured amount cannot exceed the
originally pre-authorized amount — any difference is automatically released back to the cardholder
(e.g. a €200 pre-auth with a €150 capture releases the remaining €50).

```java
// Complete — capture up to the pre-authorized amount
Intent intent = new Intent(this, PreAuthCompleteOrCancelActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_AMOUNT, 150.00f);
intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, originalOrderId);
intent.putExtra(MyPos.INTENT_EXTRA_IS_PRE_AUTH_CANCELLATION, false);
startActivityForResult(intent, MyPos.REQUEST_CODE_PRE_AUTHORIZATION_COMPLETE);
```

```java
// Cancel — release the entire hold
Intent intent = new Intent(this, PreAuthCompleteOrCancelActivity.class);
intent.putExtra(MyPos.INTENT_EXTRA_ORDER_ID, originalOrderId);
intent.putExtra(MyPos.INTENT_EXTRA_IS_PRE_AUTH_CANCELLATION, true);
startActivityForResult(intent, MyPos.REQUEST_CODE_PRE_AUTHORIZATION_CANCEL);
```

Both results are read the same way — check `MyPos.INTENT_EXTRA_STATUS` in `onActivityResult`.

## Check Pre-Authorization Status

```java
IPCPreAuthStatus ipcGetPreAuthStatus = new IPCPreAuthStatus();
ipcGetPreAuthStatus.setOrderId(originalOrderId);
ipcGetPreAuthStatus.setOnCommandCompleteListener(new IPCPreAuthStatus.OnCommandCompleteListener() {
    @Override
    public void onCommandCompleted(int preAuthStatus, String expDate) { }

    @Override
    public void onError(int status) { }
});
ipcGetPreAuthStatus.sendRequest();
```

Status constants: `MyPos.PREAUTHORIZATION_PENDING`, `MyPos.PREAUTHORIZATION_CAPTURED`,
`MyPos.PREAUTHORIZATION_DECLINED`, `MyPos.PREAUTHORIZATION_REVERSED`,
`MyPos.PREAUTHORIZATION_CANCELLED`.

## Reading Order

1. `references/setup.md` — initialize the SDK first.
2. This file — hold, complete, cancel, and check status.

## See Also

- references/store-card.md
- references/transaction-status.md
