# FastAPI-Authix 🧩

A library that provides tools for easy authentication and authorization in your FastAPI project.
It takes care of all the "boring" and repetitive stuff,
giving you more room to focus on the creative parts of your code.
Plus, it requires minimal setup! ⚡

## Features ✨
- **Simple to use**: Get started quickly without headaches. 🛠️
- **Encryption support**: Currently supports Fernet, with more algorithms coming soon! 🔐
- **Payload serialization**: Store and retrieve data objects directly in tokens — no extra work needed. 📦

## Quickstart
```python
from fastapi import FastAPI, Depends
from fastapi.responses import RedirectResponse, JSONResponse
from fastapi_authix.vault import Vault

vault, app = Vault(), FastAPI()


@app.get("/login")
async def log_in(name: str) -> RedirectResponse:
    # Any dictionary, dataclass, or model is acceptable.
    user_data = {"id": 1, "is_admin": True, "name": name}
    response = RedirectResponse("/home", status_code=302)
    # Set the access token in cookies and return a prepared response.
    vault.set_access_token(response, payload=user_data)
    return response


@app.get("/home")
async def show_home_page(user_data: dict = Depends(vault.require_data)) -> JSONResponse:
    # The original object and its data have been restored!
    print("User data: {}".format(user_data))
    return JSONResponse({"hello": user_data["name"]})

```

## Contributing 🤝
Want to contribute? Awesome! 🎉 Please open an issue first so we can discuss your ideas.
Let’s build something great together! 💡