Use the `StorageImpl` class to access your data in InCountry Platofrm using Java SDK.
```java
public class StorageImpl implements Storage {
  /**
   * creating Storage instance
   *
   * @param environmentID     Required to be passed in, or as the INC_API_KEY environment variable
                              with {@link #getInstance()}
   * @param apiKey            Required to be passed in, or as the INC_ENVIRONMENT_ID environment variable
                              with {@link #getInstance()}
   * @param endpoint          Optional. Defines API URL.
   *                          Default endpoint will be used if this parameter is null
   * @param secretKeyAccessor Instance of SecretKeyAccessor class. Used to fetch encryption secret
   * @return instance of Storage
   * @throws StorageClientException if configuration validation finishes with errors
   * @throws StorageServerException if server connection fails or server response error is returned
   */
  public static Storage getInstance(String environmentID, String apiKey, String endpoint,
                            SecretKeyAccessor secretKeyAccessor) throws StorageServerException {...}
//...
}
```

The `environmentID` and `apiKey` parameters (or `clientId` and `clientSecret` instead of `apiKey`) can be fetched from your dashboard on the Incountry Portal.

You can disable encryption (not recommended) by passing the `null` value to the `secretKeyAccessor` parameter.

Below you can find the example of how to create a new storage instance:
```java
SecretKeyAccessor secretKeyAccessor = () -> SecretsDataGenerator.fromPassword("<password>");
String endPoint = "https://us-mt-01.api.incountry.io";
String envId = "<env_id>";
String apiKey = "<api_key>";
Storage storage = StorageImpl.getInstance(envId, apiKey, endPoint, secretKeyAccessor);
```

#### oAuth Authentication

SDK also supports oAuth authentication credentials instead of plain API key authorization. oAuth authentication flow is mutually exclusive with API key authentication - you will need to provide either API key or oAuth credentials.

Below you can find the example of how to create a new storage instance with oAuth credentials (and also provide a custom oAuth endpoint):
```java
Map<String, String> authEndpointsMap = new HashMap<>();
authEndpointsMap.put("emea", "https://auth-server-emea.com");
authEndpointsMap.put("apac", "https://auth-server-apac.com");
authEndpointsMap.put("amer", "https://auth-server-amer.com");

StorageConfig config = new StorageConfig()
   //can be also set via environment variable INC_CLIENT_ID with {@link #getInstance()}
   .setClientId(CLIENT_ID)
   //can be also set via environment variable INC_CLIENT_SECRET with {@link #getInstance()}
   .setClientSecret(SECRET)
   .setAuthEndpoints(authEndpointsMap)
   .setDefaultAuthEndpoint("https://auth-server-default.com")
   .setEndpointMask(ENDPOINT_MASK)
   .setEnvId(ENV_ID);
Storage storage = StorageImpl.getInstance(config);
```

Note: the endpointMask parameter is used for switching from the default InCountry host list (api.incountry.io) to a different one. For example the `endpointMask`==`-private.incountry.io` setting will further redirect requests to `https://{COUNTRY_CODE}-private.incountry.io`
If your PoPAPI configuration relies on a custom PoPAPI server (rather than the default one) use the `countriesEndpoint` option to specify the endpoint responsible for fetching the list of supported countries.
```java
StorageConfig config = new StorageConfig()
   .setCountriesEndpoint(countriesEndpoint)
   //...
Storage storage = StorageImpl.getInstance(config);
```

### Encryption key/secret

SDK provides the `SecretKeyAccessor` interface which allows you to pass your own secrets/keys to the SDK.
```java
/**
 * Secrets accessor. Method {@link SecretKeyAccessor#getSecretsData()} is invoked on each encryption/decryption.
 */
public interface SecretKeyAccessor {

    /**
     * get your container with secrets
     *
     * @return SecretsData
     * @throws StorageClientException when something goes wrong during getting secrets
     */
    SecretsData getSecretsData() throws StorageClientException;
}


public class SecretsData {
    /**
     * creates a container with secrets
     *
     * @param secrets non-empty list of secrets. One of the secrets must have
     *        same version as currentVersion in SecretsData
     * @param currentVersion Should be a non-negative integer
     * @throws StorageClientException when parameter validation fails
     */
     public SecretsData(List<SecretKey> secrets, int currentVersion)
                throws StorageClientException {...}
    //...
}


public class SecretKey {
    /**
    * Creates a secret key
    *
    * @param secret  secret/key
    * @param version secret version, should be a non-negative integer
    * @param isKey   should be True only for user-defined encryption keys
    * @throws StorageClientException when parameter validation fails
    */
    public SecretKey(String secret, int version, boolean isKey)
              throws StorageClientException {...}
    //...
}
```

You can implement `SecretKeyAccessor` interface and pass secrets/keys in multiple ways:

1. As a constant SecretsData object:
    ```java
    SecretsData secretsData = new SecretsData(secretsList, currentVersion);
    SecretKeyAccessor accessor = () -> secretsData;
    ```

2. As a function that dynamically fetches secrets:
    ```java
    SecretKeyAccessor accessor = () -> loadSecretsData();

    private SecretsData loadSecretsData()  {
       String url = "<your_secret_url>";
       String responseJson = loadFromUrl(url).asJson();
       return SecretsDataGenerator.fromJson(responseJson);
    }
    ```

You can also use the `SecretsDataGenerator` class for creating `SecretsData` instances:

1. from a String password:
    ```java
    SecretsData secretsData = SecretsDataGenerator.fromPassword("<password>");
    ```

2. from a JSON string representing SecretsData object:
    ```java
    SecretsData secretsData = SecretsDataGenerator.fromJson(jsonString);
    ```

    ```javascript
    {
    "secrets": [
        {
        "secret": "secret0",
        "version": 0,
        "isKey": false
        },
        {
        "secret": "secret1",
        "version": 1,
        "isKey": false
        }
    ],
    "currentVersion": 1
    }
    ```

The `SecretsData` object allows you to specify multiple keys/secrets which SDK will use for data decryption based on the version of the key or secret used for data encryption.