> TWINT: https://developers.mypos.com/sdks/smart-sdk/twint
> Terminal management: https://developers.mypos.com/sdks/smart-sdk/smart-terminal-management
> Last transaction: https://developers.mypos.com/sdks/smart-sdk/get-last-transaction-data

## TWINT Payment

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(25.00)
    .currency(Currency.CHF)      // TWINT requires CHF
    .foreignTransactionId(UUID.randomUUID().toString())
    .paymentMethod(MyPOSUtil.PAYMENT_METHOD_TWINT)
    .build();

MyPOSAPI.openPaymentActivity(this, payment, PAYMENT_REQUEST_CODE);
```

## Tipping Mode

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(20.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .tippingModeEnabled(true)    // customer enters tip on terminal
    .build();
```

## MOTO Transaction (Mail / Telephone Order)

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(100.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .isMOTO(true)
    .build();
```

## Installments

```java
MyPOSPayment payment = MyPOSPayment.builder()
    .productAmount(300.00)
    .currency(Currency.EUR)
    .foreignTransactionId(UUID.randomUUID().toString())
    .installmentsNumber(3)
    .build();
```

## Get Last Transaction Data

```java
MyPOSAPI.getLastTransactionData(this, new OnLastTransactionDataListener() {
    @Override
    public void onReceive(LastTransactionData data) {
        String ref = data.getTransactionReference();
        String status = data.getTransactionStatus();
    }
});
```

## Reading Order

1. `references/card-payment.md` — basic payment builder.
2. This file — advanced payment options.

## See Also

- references/card-payment.md
- references/receipts.md
- https://developers.mypos.com/sdks/smart-sdk/smart-terminal-management
