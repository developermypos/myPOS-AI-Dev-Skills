> Docs: https://developers.mypos.com/apis/checkout-api/checkout-getting-started/authentication
> Implementation basics: https://developers.mypos.com/apis/checkout-api/checkout-getting-started/implementation-basics

## Credentials

Obtain one of the following from the myPOS merchant portal:

**Option A — Config Pack (recommended)**
A Base64-encoded bundle containing SID, WalletNumber (ClientNumber), KeyIndex, and the RSA key pair. The SDKs (PHP, Java) can parse this directly; for direct HTTP you decode it and extract the values manually.

**Option B — Individual credentials**
- Private key: RSA-2048 PEM file (your signing key — keep secret)
- API public key: PEM file (myPOS's key for verifying their responses)
- SID: Store ID (numeric)
- WalletNumber: Client/Wallet number (numeric)
- KeyIndex: Identifies which key pair is active (numeric, usually 1)

Store private key files **outside the web root** and never commit them to version control.

## Common Parameters in Every Request

Every request to the Checkout API must include these fields, regardless of operation:

| Parameter | Type | Description |
|-----------|------|-------------|
| `IPCmethod` | String | Operation name, e.g. `"IPCPurchase"` |
| `IPCVersion` | String | `"1.4"` (use `"1.4.1"` to send PartnerID/ApplicationID) |
| `IPCLanguage` | String | 2-char language code: `EN`, `DE`, `FR`, `BG`, `ES`, `IT`, `NL`, `EL`, `PT`, `SV` |
| `SID` | String | Your Store ID |
| `WalletNumber` | String | Your Client/Wallet number |
| `KeyIndex` | String | Active key index |
| `Signature` | String | Base64 RSA-SHA256 signature of all parameters — see `references/authentication.md` |

For Partner Program integrations (IPCVersion 1.4.1), also include:

| Parameter | Description |
|-----------|-------------|
| `PartnerID` | Format: `mps-p-XXXXXXXX` |
| `ApplicationID` | Format: `mps-app-XXXXXXXX` |

## Request Transport

| Flow | Sandbox URL | Production URL |
|------|------------|---------------|
| Redirect (IPCPurchase, IPCPreAuthorization) | `https://www.mypos.com/vmp/checkout-test` | `https://www.mypos.com/vmp/checkout` |
| Server-to-server (IPCIAPurchase, IPCRefund, etc.) | `https://www.mypos.com/vmp/checkout-api` | `https://www.mypos.com/vmp/checkout-api` |

Redirect flows POST as an HTML form; the browser navigates to the myPOS checkout page. Server-to-server flows POST `application/x-www-form-urlencoded` directly from your server.

**Response format**: XML by default; append `OutputFormat=1` to receive JSON.

## Response Structure

Every response includes:

| Field | Description |
|-------|-------------|
| `Status` | `0` = success; any other value = error code |
| `StatusMsg` | Human-readable status description |
| `Signature` | myPOS signature over response params — always verify |

Check `Status == 0` and verify the response `Signature` before trusting any response data.

## Reading Order

1. This file — credentials and request structure
2. `references/authentication.md` — how to compute `Signature`
3. Your operation's reference file

## See Also

- references/authentication.md
- references/testing.md
