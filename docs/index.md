## Introduction 📚
FastAPI-Authix is a lightweight authentication library for FastAPI, which handles token-based authentication,
secure data storage, and authorization flows.

## Core Features 🔑
- **Token Management**: Secure token generation, validation and storage
- **Data Serialization**: Automatic serialization of Python objects (dicts, dataclasses, models) into tokens
- **Modern Encryption**: Built-in Fernet cipher for token security (more algorithms coming soon)
- **FastAPI Integration**: Native FastAPI dependency system support

## Installation 🛠️
=== "pip"
    ```shell
    pip install fastapi-authix
    ```

=== "uv"
    ```shell
    uv add fastapi-authix
    ```

=== "poetry"
    ```shell
    poetry add fastapi-authix
    ```

=== "pipenv"
    ```shell
    pipenv install fastapi-authix
    ```

=== "fext"
    ```shell
    fext i fastapi-authix
    ```
