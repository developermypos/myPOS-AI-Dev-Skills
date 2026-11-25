> Docs: https://developers.mypos.com/apis/identity

Authentication is a 2-step process. Step 1 exchanges partner credentials for a Bearer token. Step 2 exchanges that Bearer token plus merchant credentials for a session token. Both tokens are required on every API request.

## Step 1 — OAuth2 Bearer Token (valid 1 hour)

```http
POST /api/v1/oauth/token
Host: demo-api-gateway.mypos.com
Content-Type: application/x-www-form-urlencoded

client_id=client_YOUR_PARTNER_CLIENT_ID&client_secret=secret_YOUR_PARTNER_SECRET&grant_type=client_credentials
```

```python
import requests, os

resp = requests.post(
    "https://demo-api-gateway.mypos.com/api/v1/oauth/token",
    data={
        "client_id":     os.environ["MYPOS_PARTNER_CLIENT_ID"],
        "client_secret": os.environ["MYPOS_PARTNER_CLIENT_SECRET"],
        "grant_type":    "client_credentials",
    },
    headers={"Content-Type": "application/x-www-form-urlencoded"},
)
bearer_token = resp.json()["access_token"]  # cache; expires in 3600s
```

## Step 2 — Merchant Session Token (valid 5 minutes)

```http
POST /api/v1/auth/session
Host: demo-api-gateway.mypos.com
Authorization: Bearer <bearer_token>
Content-Type: application/json

{
  "client_id": "cli_YOUR_MERCHANT_CLIENT_ID",
  "client_secret": "sec_YOUR_MERCHANT_SECRET"
}
```

```python
resp = requests.post(
    "https://demo-api-gateway.mypos.com/api/v1/auth/session",
    json={
        "client_id":     os.environ["MYPOS_MERCHANT_CLIENT_ID"],
        "client_secret": os.environ["MYPOS_MERCHANT_CLIENT_SECRET"],
    },
    headers={"Authorization": f"Bearer {bearer_token}"},
)
session_id = resp.json()["session_id"]  # expires in 300s; refresh on 401
```

> 3 consecutive failed session attempts lock the account temporarily.

## Building the Request Headers

```python
def api_headers(bearer_token: str, session_id: str) -> dict:
    return {
        "Authorization":    f"Bearer {bearer_token}",
        "X-Session":        session_id,
        "X-Partner-Id":     os.environ["MYPOS_PARTNER_ID"],
        "X-Application-Id": os.environ["MYPOS_APPLICATION_ID"],
        "Content-Type":     "application/json; x-api-version=1",
    }
```

## Token Lifecycle and Refresh

```python
class MyPOSAuth:
    def __init__(self):
        self._bearer = None
        self._session = None

    def get_headers(self) -> dict:
        # Lazy-init Bearer token (1h lifetime)
        if not self._bearer:
            self._bearer = self._fetch_bearer()
        # Lazy-init session (5 min lifetime)
        if not self._session:
            self._session = self._fetch_session(self._bearer)
        return api_headers(self._bearer, self._session)

    def on_401(self, retry_with_new_session: bool = True):
        """Call when an API request returns 401."""
        self._session = None  # always refresh session first
        if not retry_with_new_session:
            self._bearer = None  # Bearer also expired — redo OAuth

    def _fetch_bearer(self) -> str:
        # ... POST /api/v1/oauth/token ...

    def _fetch_session(self, bearer: str) -> str:
        # ... POST /api/v1/auth/session ...
```

## Reading Order

1. `references/setup.md` — credentials and environment setup
2. This file — token acquisition and header construction
3. Your sub-API reference file (epos-payments, ecommerce, banking)

## See Also

- references/setup.md
- references/testing.md
