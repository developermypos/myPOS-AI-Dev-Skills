> Docs: https://developers.mypos.com/apis/banking

The Banking API provides read access to merchant account balances, transaction history, and downloadable statements. All endpoints are GET except statement generation.

## List Merchant Accounts

```python
response = requests.get(
    f"{BASE_URL}/accounting/v1/accounts",
    params={"page": 1, "size": 20},
    headers=auth.get_headers(),
)
accounts = response.json()["items"]
# Each account: account_number, iban, currency, account_name, is_reserve, is_suspense
```

Merchants typically have separate accounts per currency (EUR, GBP, etc.) and may have reserve or suspense accounts.

## Get Transaction History

```python
# All transactions (paginated)
response = requests.get(
    f"{BASE_URL}/accounting/v1/transactions",
    params={"page": 1, "size": 50},
    headers=auth.get_headers(),
)
transactions = response.json()["items"]
# has_next_page, total_pages, total_count also returned

# Single transaction by payment reference
response = requests.get(
    f"{BASE_URL}/accounting/v1/transactions/{payment_reference}",
    headers=auth.get_headers(),
)
tx = response.json()
# Fields: payment_reference, amount, currency, status, created_at, updated_at, description
```

## Batch Transaction Lookup

Retrieve multiple transactions in one call:

```python
response = requests.get(
    f"{BASE_URL}/accounting/v1/transactions/multiple",
    params={"references": "REF001,REF002,REF003"},
    headers=auth.get_headers(),
)
transactions = response.json()["items"]
```

## Download Account Statements

```python
response = requests.post(
    f"{BASE_URL}/accounting/v1/accounts/statements",
    json={
        "account_number": "YOUR_ACCOUNT_NUMBER",
        "document_type":  "MT940MultiCash",  # or "SWIFT"
        "date":           "2024-03-01",       # yyyy-mm-dd
    },
    headers=auth.get_headers(),
)
# Response: statement file content or download URL
```

Supported formats: `MT940MultiCash` (standard banking format for accounting software), `SWIFT`.

## Pagination Pattern

All list endpoints return:

```json
{
  "items": [...],
  "page": 1,
  "page_size": 20,
  "total_pages": 5,
  "total_count": 98,
  "has_previous_page": false,
  "has_next_page": true
}
```

```python
def fetch_all_transactions(auth):
    page, all_txns = 1, []
    while True:
        resp = requests.get(f"{BASE_URL}/accounting/v1/transactions",
                            params={"page": page, "size": 100},
                            headers=auth.get_headers()).json()
        all_txns.extend(resp["items"])
        if not resp["has_next_page"]:
            break
        page += 1
    return all_txns
```

## Reading Order

1. `references/authentication.md` — get tokens and headers first
2. This file — account and transaction data

## See Also

- references/authentication.md
- references/ecommerce.md
