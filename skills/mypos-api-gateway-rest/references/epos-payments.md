> Docs: https://developers.mypos.com/apis/epos

The ePOS payments API triggers and manages payments on a physical myPOS terminal connected to the merchant's account. The terminal handles all card interaction.

## Create a Payment

```http
POST /epos/v1/payments
```

```python
response = requests.post(
    f"{BASE_URL}/epos/v1/payments",
    json={
        "reference":    f"order-{uuid.uuid4()}",  # your unique reference
        "amount":       2345,                      # in minor currency units (2345 = 23.45 EUR)
        "currency":     "EUR",
        "terminal_id":  "TERMINAL_SERIAL_NUMBER",
        "description":  "Order #1001",
        "operator_code": "01",                     # optional cashier identifier
    },
    headers=auth.get_headers(),
)
payment = response.json()
payment_id = payment["id"]   # store for status polling
```

Response includes `id`, `status`, `amount`, `currency`, and timestamps.

## Poll Payment Status

```python
response = requests.get(
    f"{BASE_URL}/epos/v1/payments/{payment_id}",
    headers=auth.get_headers(),
)
status = response.json()["status"]  # e.g. "approved", "declined", "pending"
```

Poll until status is `"approved"` or `"declined"`. Terminal interaction is asynchronous.

## Reverse a Payment

Cancel a payment that is still in progress or not yet settled:

```python
response = requests.post(
    f"{BASE_URL}/epos/v1/payments/{payment_id}/reversal",
    headers=auth.get_headers(),
)
```

## Refund a Payment

Issue a refund for a completed (settled) payment:

```python
response = requests.post(
    f"{BASE_URL}/epos/v1/payments/refund",
    json={
        "payment_id": payment_id,
        "amount":     1000,   # partial refund in minor units
        "currency":   "EUR",
    },
    headers=auth.get_headers(),
)
```

## Amount Format

All amounts are in **minor currency units** (integer): `EUR 23.45` = `2345`, `GBP 10.00` = `1000`.

## Reading Order

1. `references/authentication.md` — get tokens and headers first
2. This file — create and manage payments on terminal
3. `references/epos-terminals.md` — list available terminal IDs

## See Also

- references/epos-terminals.md
- references/authentication.md
