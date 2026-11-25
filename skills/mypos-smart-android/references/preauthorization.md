> Docs: https://developers.mypos.com/sdks/smart-sdk/pre-authorization

## Pre-Authorize (Hold Funds)

```java
private static final int PREAUTH_REQUEST_CODE = 1004;

MyPOSPreauthorization preauth = MyPOSPreauthorization.builder()
    .productAmount(100.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .build();

MyPOSAPI.openPreauthorizationActivity(this, preauth, PREAUTH_REQUEST_CODE);
```

## Complete Pre-Authorization (Capture)

```java
private static final int PREAUTH_COMPLETE_CODE = 1005;

MyPOSPreauthorizationCompletion completion = MyPOSPreauthorizationCompletion.builder()
    .productAmount(85.00)     // can be less than the authorized amount
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(preauthRef)
    .build();

MyPOSAPI.openPreauthorizationCompletionActivity(this, completion, PREAUTH_COMPLETE_CODE);
```

## Cancel Pre-Authorization

```java
private static final int PREAUTH_CANCEL_CODE = 1006;

MyPOSPreauthorizationCancellation cancellation = MyPOSPreauthorizationCancellation.builder()
    .foreignTransactionId(UUID.randomUUID().toString())
    .originalTransactionReference(preauthRef)
    .build();

MyPOSAPI.openPreauthorizationCancellationActivity(this, cancellation, PREAUTH_CANCEL_CODE);
```

## Reading Order

1. `references/card-payment.md` — understand the basic payment pattern first.
2. This file — pre-authorization.

## See Also

- references/card-payment.md
