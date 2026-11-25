> Docs: https://developers.mypos.com/apis/checkout-api/checkout-getting-started/authentication
> Security: https://developers.mypos.com/apis/checkout-api/checkout-getting-started/security-and-availability

The Checkout API uses RSA-2048 digital signatures. Every request you send must include a `Signature` parameter, and every response from myPOS includes one you must verify.

## Computing the Request Signature

```
Step 1: Collect all parameters you are sending in the request
        (every field except Signature itself)

Step 2: Sort the parameter names alphabetically (case-sensitive, A-Z)

Step 3: Concatenate the values in that sorted order
        — values only, no keys, no delimiters

Step 4: Hash the concatenated string with SHA-256

Step 5: Sign the hash using RSA-PKCS#1 v1.5 with your private key (RSA-2048)

Step 6: Base64-encode the raw signature bytes → this is your Signature value
```

### Pseudocode

```python
import hashlib, base64
from cryptography.hazmat.primitives import hashes, serialization
from cryptography.hazmat.primitives.asymmetric import padding

def compute_signature(params: dict, private_key_pem: bytes) -> str:
    # Step 1-2: sort by key
    sorted_values = [str(params[k]) for k in sorted(params.keys())]
    # Step 3: concatenate values
    concatenated = "".join(sorted_values)
    # Step 4: SHA-256 hash
    digest = hashlib.sha256(concatenated.encode("utf-8")).digest()
    # Step 5: RSA sign
    private_key = serialization.load_pem_private_key(private_key_pem, password=None)
    signature_bytes = private_key.sign(digest, padding.PKCS1v15(), hashes.Prehashed(hashes.SHA256()))
    # Step 6: Base64 encode
    return base64.b64encode(signature_bytes).decode("utf-8")
```

The same algorithm applies in any language — sort parameter keys, join values, SHA-256, RSA-PKCS1v15 sign, Base64 encode.

### Example (Ruby)

```ruby
require 'openssl'
require 'base64'

def compute_signature(params, private_key_pem)
  sorted_values = params.sort_by { |k, _| k }.map { |_, v| v.to_s }.join
  digest = OpenSSL::Digest::SHA256.digest(sorted_values)
  private_key = OpenSSL::PKey::RSA.new(private_key_pem)
  Base64.strict_encode64(private_key.private_encrypt(digest, OpenSSL::PKey::RSA::PKCS1_PADDING))
end
```

### Example (Java)

```java
import java.security.*;
import java.util.*;
import java.util.Base64;

public static String computeSignature(Map<String, String> params, PrivateKey privateKey) throws Exception {
    List<String> sortedKeys = new ArrayList<>(params.keySet());
    Collections.sort(sortedKeys);
    StringBuilder concat = new StringBuilder();
    for (String key : sortedKeys) concat.append(params.get(key));

    MessageDigest sha256 = MessageDigest.getInstance("SHA-256");
    byte[] hash = sha256.digest(concat.toString().getBytes("UTF-8"));

    Signature rsa = Signature.getInstance("SHA256withRSA");
    rsa.initSign(privateKey);
    rsa.update(hash);
    return Base64.getEncoder().encodeToString(rsa.sign());
}
```

## Verifying the Response Signature

myPOS signs every response using their private key. Verify with their public key:

```python
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes, serialization
import base64, hashlib

def verify_response_signature(params: dict, api_public_key_pem: bytes) -> bool:
    signature_b64 = params.pop("Signature")  # remove Signature from param map
    sorted_values = "".join([str(params[k]) for k in sorted(params.keys())])
    digest = hashlib.sha256(sorted_values.encode("utf-8")).digest()
    
    public_key = serialization.load_pem_public_key(api_public_key_pem)
    try:
        public_key.verify(base64.b64decode(signature_b64), digest, padding.PKCS1v15(), hashes.Prehashed(hashes.SHA256()))
        return True
    except Exception:
        return False
```

**Apply this to every response and every incoming webhook.** If verification fails, reject the data.

## Reading Order

1. `references/setup.md` — credentials and common parameters
2. This file — signature computation
3. `references/purchase.md` or your operation's reference file

## See Also

- references/setup.md
- references/webhooks.md
