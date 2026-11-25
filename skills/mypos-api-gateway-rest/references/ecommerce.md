> Docs: https://developers.mypos.com/apis/ecommerce

The eCommerce API creates hosted payment collection tools — links, buttons, and requests. The customer pays on a myPOS-hosted checkout page; no card data touches your server.

All three resource types follow the same CRUD pattern:
- `POST` to create
- `GET /{code}` to retrieve
- `PUT /{code}` to update
- `DELETE /{code}` to remove

---

## Payment Links

Shareable URLs sent via SMS, email, chat, or social media. Customer clicks the link and pays.

```python
# Create
response = requests.post(
    f"{BASE_URL}/ecommerce/v1/payment-links",
    json={
        "amount":       2345,          # minor units (2345 = 23.45)
        "currency":     "EUR",
        "description":  "Invoice #42",
        "customer_email": "buyer@example.com",
        "expiry_date":  "2024-12-31",  # ISO date
    },
    headers=auth.get_headers(),
)
link_code = response.json()["code"]
link_url  = response.json()["url"]    # share this with the customer

# List (paginated)
response = requests.get(
    f"{BASE_URL}/ecommerce/v1/payment-links",
    params={"page": 1, "size": 20},
    headers=auth.get_headers(),
)

# Get status
response = requests.get(f"{BASE_URL}/ecommerce/v1/payment-links/{link_code}", headers=auth.get_headers())
status = response.json()["status"]    # Active, Disabled, Expired, etc.

# List transactions for this link
response = requests.get(f"{BASE_URL}/ecommerce/v1/payment-links/transactions", headers=auth.get_headers())
```

---

## Payment Buttons

Embeddable HTML buttons for websites. Customer clicks the button on your page and pays on myPOS.

```python
# Create
response = requests.post(
    f"{BASE_URL}/ecommerce/v1/payment-buttons",
    json={
        "name":         "Buy Now — Product A",
        "amount":       4999,
        "currency":     "EUR",
        "language":     "EN",
        "url_cancel":   "https://example.com/cancel",
        "url_return":   "https://example.com/thank-you",
    },
    headers=auth.get_headers(),
)
button_url = response.json()["url"]   # embed as <a href=...> or <script src=...>

# Update
requests.put(f"{BASE_URL}/ecommerce/v1/payment-buttons/{code}", json={...}, headers=auth.get_headers())

# Delete
requests.delete(f"{BASE_URL}/ecommerce/v1/payment-buttons/{code}", headers=auth.get_headers())
```

---

## Payment Requests

Send a payment request directly to a customer (invoicing, remote billing). The customer receives a notification and pays online.

```python
# Create
response = requests.post(
    f"{BASE_URL}/ecommerce/v1/payment-requests",
    json={
        "amount":          7500,
        "currency":        "EUR",
        "description":     "Consulting services — March",
        "customer_email":  "client@example.com",
        "customer_name":   "Jane Smith",
        "expiry_date":     "2024-04-30",
    },
    headers=auth.get_headers(),
)
request_code = response.json()["code"]

# Get status
response = requests.get(f"{BASE_URL}/ecommerce/v1/payment-requests/{request_code}", headers=auth.get_headers())

# Send reminder
requests.post(
    f"{BASE_URL}/ecommerce/v1/payment-requests/{request_code}/reminder",
    headers=auth.get_headers(),
)
```

---

## Utility Endpoints

```python
# Supported languages (for language parameter)
requests.get(f"{BASE_URL}/ecommerce/v1/common/languages", headers=auth.get_headers())

# Settlement account info
requests.get(f"{BASE_URL}/ecommerce/v1/common/settlement-data", headers=auth.get_headers())
```

## Reading Order

1. `references/authentication.md` — get tokens and headers first
2. This file — payment collection tools

## See Also

- references/authentication.md
- references/banking.md
