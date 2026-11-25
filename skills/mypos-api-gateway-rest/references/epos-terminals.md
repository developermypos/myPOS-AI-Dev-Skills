> Docs: https://developers.mypos.com/apis/epos

Terminal management endpoints let you discover available terminals, manage their lifecycle, and retrieve receipts.

## List All Terminals

```python
response = requests.get(
    f"{BASE_URL}/pos/v1/terminals",
    headers=auth.get_headers(),
)
terminals = response.json()["items"]
# Each terminal: id, serial_number, model, status, outlet_name, availability
```

Use `status` and `availability` to find a terminal that is online and ready to accept payments.

## Get a Single Terminal

```python
response = requests.get(
    f"{BASE_URL}/pos/v1/terminals/{terminal_id}",
    headers=auth.get_headers(),
)
terminal = response.json()
```

## Activate a Terminal

```python
response = requests.post(
    f"{BASE_URL}/pos/v1/terminals/activation",
    json={
        "terminal_id":      "SERIAL_NUMBER",
        "activation_code":  "CODE_FROM_TERMINAL_SCREEN",
    },
    headers=auth.get_headers(),
)
```

The activation code is displayed on the terminal's screen during the activation flow.

## Deactivate a Terminal

```python
response = requests.post(
    f"{BASE_URL}/pos/v1/terminals/deactivation",
    json={"terminal_id": "SERIAL_NUMBER"},
    headers=auth.get_headers(),
)
```

## Get a Receipt

Retrieve a receipt for a specific payment by its reference number:

```python
response = requests.get(
    f"{BASE_URL}/pos/v1/terminals/receipt/{payment_reference}",
    headers=auth.get_headers(),
)
receipt = response.json()  # contains receipt lines, amounts, timestamps
```

## Terminal Transaction History

```python
response = requests.get(
    f"{BASE_URL}/pos/v1/terminals/{terminal_id}/transactions",
    params={"page": 1, "size": 20},
    headers=auth.get_headers(),
)
transactions = response.json()["items"]
```

## Reading Order

1. `references/authentication.md` — get tokens and headers
2. This file — discover terminal IDs and manage lifecycle
3. `references/epos-payments.md` — create payments using discovered terminal_id

## See Also

- references/epos-payments.md
