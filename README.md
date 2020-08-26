# StoredSafe Python

Transparent Python wrapper for the StoredSafe REST-like API.

Full documentation of the API response signatures and more advanced paramters can be found at the [StoredSafe API Documentation](https://developer.storedsafe.com/).


## Install

Install [storedsafe](https://pypi.org/project/storedsafe/) from pypi.
```bash
pip install storedsafe
```

## Examples

### Login

```python
# Initial configuration
api = StoredSafe(host='my.site.com', apikey='my-apikey')

# Login using TOTP
api.login_totp(username='my-username', passphrase='my-passphrase', otp='my-timed-otp')

# Login using YubiKey
api.login_yubikey(username='my-username', passphrase='my-passphrase', otp='my-yubico-otp')
```

In the event you already have a token, you can skip the previous step and input the token directly.
```python
api = StoredSafe(host='my.site.com', token='my-storedsafe-token')
```

If you're using the [StoredSafe tokenhandler](https://github.com/storedsafe/tokenhandler), you can also retrieve the host, apikey and token from an rc-file:
```python
# Default rc location
api = StoredSafe.from_rc()

# Custom rc location
api = StoredSafe.from_rc(path='/path/to/rc-file')
```

### Programming styles

For create and edit methods, parameters can be easily passed as keyword arguments, for example:
```python
api.create_vault(vaultname="My Vault", policy=7, description="Sercret")
```

Or if you're receiving data in dict-format, it can be unpacked into the method:
```python
data = function_that_returns_data()
api.create_vault(**data)
```

### Return types

The return value of all methods is a [`requests` response object](https://requests.readthedocs.io/en/latest/api/#requests.Response). To obtain the data returned by a successful response object, you can use the `json()` function:
```python
r = api.list_vaults()
if <= 403:
    data = res.json()
    if res.ok:
        print(data['VAULTS'])
    else:
        print(data['ERRORS'])
```

## Usage

```python
from storedsafe import StoredSafe

# Manual configuration
api = StoredSafe(host='my.site.com', apikey='my-apikey', token='my-storedsafe-token')

# Automatic configuration
api = StoredSafe.from_rc() # Use default path ~/.storedsafe-client.rc
api = StoredSafe.from_rc(path='/path/to/rc-file')

# Auth
api.login_totp(username='my-username', passphrase='my-passphrase', otp='my-otp')
api.login_yubikey(username='my-username', passphrase='my-passphrase', otp='my-otp')
api.logout()
api.check()

# Vaults
api.list_vaults()
api.vault_objects(vault_id) # String or integer
api.vault_members(vault_id)
api.create_vault(**params) # See parameters in API documentation
api.edit_vault(vault_id, **params)
api.delete_vault(vault_id)

# Objects
api.get_object(object_id) # String or integer
api.get_object(object_id, children=True) # children False by default
api.decrypt_object(object_id)
api.create_object(**params)
api.edit_object(object_id, **params)
api.delete_object(object_id)

# Users
api.list_users() # List all users
api.list_users(search_string) # Search for any user matching search_string
api.get_user(user_id)
api.create_user(**params)
api.edit_user(user_id, **params)
api.delete_user(user_id)

# Utils
api.status_values()
api.password_policies()
api.version()
api.generate_password() # Use default settings
api.generate_password(**params)
```
