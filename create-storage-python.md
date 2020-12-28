To access your data in InCountry Platform with Python SDK, you need to create an instance of the `Storage` class.
```python
class Storage:
    def __init__(
        self,
        environment_id: Optional[str] = None,   # Required to be passed in, or as the INC_API_KEY environment variable
        api_key: Optional[str] = None,          # Required when using API key authorization, or as an environment variable
        client_id: Optional[str] = None,        # Required when using oAuth authorization, can be also set through the INC_CLIENT_ID variable
        client_secret: Optional[str] = None,    # Required when using oAuth authorization, can be also set through the INC_CLIENT_SECRET variable
        endpoint: Optional[str] = None,         # Optional. Defines API URL. Can also be set up through the INC_ENDPOINT environment variable
        encrypt: Optional[bool] = True,         # Optional. If False, encryption is not applied
        debug: Optional[bool] = False,          # Optional. If True enables the additional debug logging
        options: Optional[Dict[str, Any]] = {}, # Optional. It is used to fine-tune some configurations
        custom_encryption_configs: Optional[List[dict]] = None, # Optional. List of custom encryption configurations
        secret_key_accessor: Optional[SecretKeyAccessor] = None, # Instance of the SecretKeyAccessor class. It is used to fetch the encryption secret
    ):
        ...
```

---
**WARNING**

API Key authorization is being deprecated. The backward compatibility is preserved for the `api_key` parameter but you no longer can access API keys (neither old nor new) from your dashboard.

---

The `client_id`, `client_secret`,  and `environment_id` variables can be fetched from your dashboard on `Incountry` Portal.


The `endpoint` variable defines API URL and is used to override the default one.

You can disable the encryption (not recommended). Set the `encrypt` property to `false` if you want to disable it. Avoid using it when using for production data.

The `options` variable allows you to tweak some SDK configurations
```python
{
    "http_options": {
        "timeout": int,         # In seconds. Should be greater than 0
    },
    "auth_endpoints": dict,     # A custom endpoints regional map that should be used for fetching oAuth tokens

    "countries_endpoint": str,  # If your PoPAPI configuration relies on a custom PoPAPI server
                                # (rather than the default one) use the `countriesEndpoint` option
                                # to specify the endpoint responsible for fetching the list of supported countries

    "endpoint_mask": str,       # Defines API base hostname part to use.
                                # If set, all requests will be sent to https://${country}${endpointMask} host
                                # instead of the default one (https://${country}-mt-01.api.incountry.io)
}
```

Below you can find the example of how to create a storage instance
```python
from incountry import Storage, SecretKeyAccessor

storage = Storage(
    api_key="<api_key>",
    environment_id="<env_id>",
    debug=True,
    secret_key_accessor=SecretKeyAccessor(lambda: "password"),
    options={
        "http_options": {
            "timeout": 5
        },
        "countries_endpoint": "https://private-pop.incountry.io/countries",
        "endpoint_mask" ".private-pop.incountry.io",
    }
)
```


#### oAuth Authentication
The SDK also supports oAuth authentication credentials instead of plain API key authorization. oAuth authentication flow is mutually exclusive with API key authentication - you need to provide either an API key or oAuth credentials.

Below you can find the example of how to create a storage instance with oAuth credentials (and also provide a custom oAuth endpoint):
```python
from incountry import Storage, SecretKeyAccessor

storage = Storage(
    client_id="<client_id>",
    client_secret="<client_secret>",
    environment_id="<env_id>",
    debug=True,
    secret_key_accessor=SecretKeyAccessor(lambda: "password"),
    options={
        "auth_endpoints": {
            "default": "https://auth-server-default.com",
            "emea": "https://auth-server-emea.com",
            "apac": "https://auth-server-apac.com",
            "amer": "https://auth-server-amer.com",
        }
    }
)
```


#### Encryption key/secret

The `secret_key_accessor` variable is used to pass a key or secret used for data encryption.

Note: even though SDK uses PBKDF2 to generate a cryptographically strong encryption key, you must ensure that you provide a secret/password which follows the modern security best practices and standards.

The `SecretKeyAccessor` class constructor allows you to pass a function that should return either a string representing your secret or a dictionary (we call it `secrets_data` object):

```python
{
  "secrets": [{
       "secret": str,
       "version": int, # Should be an integer greater than or equal to 0
       "isKey": bool,  # Should be True only for user-defined encryption keys
    }
  }, ....],
  "currentVersion": int,
}
```

The `secrets_data` variable allows you to specify multiple keys/secrets which the SDK will use for data decryption based on the version of the key or secret used for encryption. Meanwhile SDK will encrypt data by using only the key/secret pair which matches the `currentVersion` parameter provided in the `secrets_data` object.

This enables the flexibility required to support Key Rotation policies when secret/key must change with time. The SDK will encrypt data by using the current secret/key while maintaining the ability to decrypt data records that were encrypted with old key/secret. The SDK also provides a method for data migration which allows you to re-encrypt data with the newest key/secret. For details please see the `migrate` method.

The SDK allows you to use custom encryption keys, instead of secrets. Please note that a user-defined encryption key should be a 32-character 'utf8' encoded string as required by AES-256 cryptographic algorithm.


Below you can find several examples of how you can use `SecretKeyAccessor` module.
```python
# Get a secret from a variable
from incountry import SecretKeyAccessor

password = "password"
secret_key_accessor = SecretKeyAccessor(lambda: password)

# Get secrets via an HTTP request
from incountry import SecretKeyAccessor
import requests as req

def get_secrets_data():
    url = "<your_secret_url>"
    r = req.get(url)
    return r.json() # assuming response is a `secrets_data` object

secret_key_accessor = SecretKeyAccessor(get_secrets_data)
```