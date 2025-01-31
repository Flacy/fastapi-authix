The library expects broad use of various encryption algorithms in the future.
However, we couldn't leave the library without at least one encryption method.
By default, Fernet is available — it's ideal for applications without complex cryptographic requirements.
Under the hood, we use the [cryptography](https://cryptography.io/en/latest/) library,
with an external layer called **Secure Layer**.
This is a protocol (interface) for interacting with cryptographic functions.

## Fernet
Fernet is a symmetric encryption method based on **AES** in **CBC** mode with **HMAC** for data integrity.
It's secure, simple, and supports time-limited tokens.

```python
from cryptography.fernet import Fernet as Cipher
from fastapi_authix.security.fernet import Fernet

secure_layer = Fernet(
    secret_key=Cipher.generate_key(),
    time_to_live=3600,  # 1 hour
)
```
