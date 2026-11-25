> Docs: https://developers.mypos.com/apis/checkout-api/in-app-purchase

IPCIAPurchase charges a card server-to-server without redirecting the customer. Use it when you already have a stored CardToken from a previous IPCPurchase or IPCIAStoreCard, or when you are PCI SAQ-D certified and can handle raw card data.

> **PCI SAQ-D requirement**: Sending raw card data (PAN, CVC) requires PCI SAQ-D certification. If not certified, use CardToken only.

## Request with CardToken (no PCI scope)

POST to `https://www.mypos.com/vmp/checkout-api` as `application/x-www-form-urlencoded`:

```python
params = {
    "IPCmethod":    "IPCIAPurchase",
    "IPCVersion":   "1.4",
    "IPCLanguage":  "EN",
    "SID":          "YOUR_SID",
    "WalletNumber": "YOUR_WALLET",
    "KeyIndex":     "1",
    "Amount":       "25.00",
    "Currency":     "EUR",
    "OrderID":      f"order-{uuid.uuid4()}",
    "ItemName":     "Product Name",
    "CardToken":    "BASE64_TOKEN_FROM_PRIOR_PURCHASE",
    "URL_Notify":   "https://example.com/api/payment/notify",
}
params["Signature"] = compute_signature(params, private_key_pem)

response = requests.post(
    "https://www.mypos.com/vmp/checkout-api",
    data=params,
    headers={"Content-Type": "application/x-www-form-urlencoded", "User-Agent": "MyApp/1.0"}
)
```

## Request with Raw Card Data (PCI SAQ-D required)

```python
params = {
    "IPCmethod":        "IPCIAPurchase",
    "IPCVersion":       "1.4",
    "IPCLanguage":      "EN",
    "SID":              "YOUR_SID",
    "WalletNumber":     "YOUR_WALLET",
    "KeyIndex":         "1",
    "Amount":           "25.00",
    "Currency":         "EUR",
    "OrderID":          f"order-{uuid.uuid4()}",
    "ItemName":         "Product Name",
    "PAN":              "4111111111111111",   # card number
    "CardholderName":   "JANE DOE",
    "ExpDate":          "1226",               # MMYY
    "CVC":              "123",
    "ECI":              "7",                  # 7 = MOTO / non-3DS
    "URL_Notify":       "https://example.com/api/payment/notify",
    "CardTokenRequest": "1",                  # optionally store token
}
params["Signature"] = compute_signature(params, private_key_pem)
```

## Handling the Response

```python
# Parse XML response (or JSON if OutputFormat=1 appended)
import xml.etree.ElementTree as ET

root = ET.fromstring(response.text)
status = root.find("Status").text
if status == "0":
    ipc_trnref = root.find("IPC_Trnref").text
    card_token  = root.find("CardToken").text  # present if CardTokenRequest was set
    # verify response Signature before trusting
    verify_response_signature(xml_params_as_dict, api_public_key_pem)
else:
    error_msg = root.find("StatusMsg").text
    # handle error
```

## Reading Order

1. `references/setup.md` — credentials
2. `references/authentication.md` — signature computation
3. This file — server-side payment
4. `references/webhooks.md` — IPCPurchaseNotify arrives even for server-side payments

## See Also

- references/store-card.md
- references/authorization.md
- references/webhooks.md
