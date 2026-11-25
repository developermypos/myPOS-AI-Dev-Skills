> Docs: https://developers.mypos.com/sdks/api-gateway-typescript-sdk/api-reference

## Result Pattern

The SDK **never throws** on API errors. Every call returns an `ApiResult<T>` discriminated union:

```ts
type ApiResult<T> =
  | { success: true; data: T }
  | { success: false; error: ApiError };
```

Always check `result.success` before using the data:

```ts
const result = await client.banking.getAccounts();

if (!result.success) {
  // result.error contains: { code, message, details? }
  console.error(`API error ${result.error.code}: ${result.error.message}`);
  return;
}

// Safe to use result.data here
const { accounts } = result.data;
```

## Typed Error Access

```ts
if (!result.success) {
  const { code, message } = result.error;
  // HTTP-level errors: 400, 401, 403, 404, 422, 500
  // code is the error type string from the API
  throw new Error(`myPOS API error (${code}): ${message}`);
}
```

## Common Error Codes

| Code | Meaning |
|------|---------|
| `UNAUTHORIZED` | Invalid or expired OAuth token — re-initialize gateway |
| `SESSION_EXPIRED` | Session token expired — the SDK refreshes automatically |
| `NOT_FOUND` | Resource does not exist |
| `VALIDATION_ERROR` | Request parameters are invalid |
| `INTERNAL_ERROR` | Server-side error — retry with backoff |

## Reading Order

1. `references/authentication.md` — understand the SDK structure.
2. This file — apply the result pattern to every API call.

## See Also

- references/authentication.md
