> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/sample-code/banking
> API reference: https://developers.mypos.com/apis/banking

## Get Merchant Accounts

```ts
const result = await client.banking.getAccounts();
if (!result.success) {
  console.error('Failed to get accounts:', result.error);
  return;
}
console.log(result.data.accounts);
```

## Get Transaction List

```ts
const result = await client.banking.getTransactions({
  from: '2024-01-01',
  to: '2024-12-31',
  page: 1,
  pageSize: 50,
});
if (result.success) {
  console.log(result.data.transactions);
}
```

## Get Transaction Details

```ts
const result = await client.banking.getTransactionDetails(paymentReference);
if (result.success) {
  console.log(result.data.transaction);
}
```

## Generate Account Statement

```ts
const result = await client.banking.generateStatement({
  accountId: 'ACC123',
  from: '2024-01-01',
  to: '2024-03-31',
  format: 'PDF',
});
```

## API Endpoints

| Operation | URL |
|-----------|-----|
| List accounts | https://developers.mypos.com/apis/banking/account/get-merchant-accounts-related-to-the-input-session |
| List transactions | https://developers.mypos.com/apis/banking/transaction/get-merchant-transactions-list |
| Transaction details | https://developers.mypos.com/apis/banking/transaction/get-merchant-transaction-details-based-on-payment-reference |
| Generate statement | https://developers.mypos.com/apis/banking/account/generate-account-statement-file-based-on-input-date |

## Reading Order

1. `references/authentication.md` — initialize SDK first.
2. This file — Banking API calls.
3. `references/error-handling.md` — handle `result.success === false`.

## See Also

- references/authentication.md
- references/error-handling.md
- references/multi-merchant.md
