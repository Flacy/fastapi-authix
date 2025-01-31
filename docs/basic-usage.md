## Getting Started
### Vault
Everything starts with `Vault`. It handles all interactions with other classes in the correct order.
Setting it up is straightforward since all parameters have **default values**.
It's ready for production use out of the box, though you'll likely want to customize it for your needs.

```python
from fastapi_authix.vault import Vault

vault = Vault()
```

Let's explore its configuration options:

#### cipher
The security layer responsible for data encryption and access token generation.
By default, it uses the [Fernet](https://cryptography.io/en/latest/fernet/) encryption algorithm with a key that's
**generated on each application startup**.
You can choose from other [available ciphers](/ciphers) or implement your own.

#### serializer
The serializer converts objects into JSON format, which is then encoded into access tokens and reconstructed during decryption.
The library [supports](/serializers) serialization of most common data models.

!!! Info
    By default, the serializer runs in `auto` mode, automatically detecting your data types.
    This requires a one-time detection process.
!!! Warning
    Using incorrect data types will raise an exception during serialization.


#### cookie_key, header_key
The name of the cookie (or header) where the access token will be stored.

#### use_cookie, use_header
Tells Vault whether to check for access tokens in cookies and/or headers.
These settings work independently — enabling both means tokens will be checked in both places.
Cookies take precedence when both are enabled.

## Building an Application
Let's create a simple application to demonstrate how to use Vault:

```python
from starlette import status
from fastapi import FastAPI, Depends
from fastapi.responses import RedirectResponse, JSONResponse
from fastapi_authix.vault import Vault

app, vault = FastAPI(), Vault(serializer="dict")


@app.get("/login")
async def login(name: str) -> RedirectResponse:
    response = RedirectResponse("/home", status_code=status.HTTP_302_FOUND)
    vault.set_access_token(response, payload={"name": name})
    return response


@app.get("/home")
async def home_page(user_data: dict[str, str] = Depends(vault.require_data)) -> JSONResponse:
    return JSONResponse({"hello": user_data["name"]})
```

Running this example:

```shell
$ curl -L -c cookies.txt -b cookies.txt http://localhost:8000/login?name=John
{"hello":"John"}
```

In this example, we:

1. Set a payload `{"name": "John"}`
2. Serialize it to JSON and encrypt it into an access token
3. Redirect to `/home`, which requires the access token from cookies
4. Decrypt and restore the data

That's it! A simple but complete authentication flow.

## Token Deactivation  
Let's build on the previous example and add a new endpoint.

```python
@app.get("/logout")
async def logout(
    access_token: str = Depends(vault.extract_access_token),
    _: dict[str, str] = Depends(vault.require_data),
) -> JSONResponse:
    response = JSONResponse({"ok": True})
    vault.disable_access_token(access_token)
    vault.remove_access_token(response)
    return response
```

In this example, we:

1. Extract the access token from cookies.
2. Validate the token using the `require_data` dependency.
   This is necessary because `extract_access_token` does not perform validation — it only retrieves the token from the request.
3. Remove the access token from cookies.
4. Return a response to the client.

### Token Lifetime
Each token has a lifetime _(by default, it's implicitly set to 1 hour)_.
However, when deactivated, the token is added to a special store,
where it remains until reused or the application restarts.
This ensures that if someone tries to authenticate with a deactivated token, the system will reject it.

### Expired Tokens
If a token is not deactivated, it will expire on its own and become unusable.
In that case, you will receive an exception related to your cipher.

!!! Note  
    The library doesn’t catch cryptographic errors.
    Different encryption methods may throw different exceptions.