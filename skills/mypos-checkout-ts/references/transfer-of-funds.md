> Request money: https://developers.mypos.com/apis/checkout-api/request-money
> Send money: https://developers.mypos.com/apis/checkout-api/send-money
> Mandate: https://developers.mypos.com/apis/checkout-api/mandate-management

## Send Money

**Prior approval required.** Send Money must be enabled on your account before use. Contact myPOS at online@mypos.com to request activation.

Transfer funds from your myPOS account to another myPOS account by wallet number. No mandate required.

```ts
const result = await mypos.sendMoney({
  customerWalletNumber: 'RECIPIENT_WALLET_NUMBER',
  amount: 50.00,
  currency: 'EUR',
  transactionReference: `transfer-${Date.now()}`,  // must be unique per transfer
  reason: 'Invoice settlement',
});

if (!result.success) {
  console.error('Transfer failed:', result.error);
  return;
}
```

## Request Money

Send a payment request to a customer — they pay via a link. Requires a mandate (direct debit agreement) to be signed by the customer first.

```ts
const result = await mypos.requestMoney({
  recipientEmail: 'customer@example.com',
  amount: 75.00,
  currency: 'EUR',
  orderReference: `req-${Date.now()}`,
  description: 'Invoice #3001',
});
```

## Create a Mandate (Direct Debit Agreement — Request Money only)

A mandate is required for Request Money only. It authorises the merchant to pull funds from a customer's myPOS account. It is not used for Send Money.

```ts
const fields = mypos.generateMandateFields(
  `customer-${customerId}`,
  {
    urlOk: 'https://example.com/mandate/signed',
    urlCancel: 'https://example.com/mandate/cancel',
    urlNotify: 'https://example.com/api/mypos/notify',
  }
);
// Render with <PaymentForm> — customer signs the mandate on myPOS hosted page
// Save the mandateReference received via IPN — required for every requestMoney call
```

## Reading Order

1. `references/setup.md` — initialize first.
2. This file — TOF flows.
3. `references/webhooks.md` — IPN handling for mandate signature notifications.

## See Also

- references/webhooks.md
