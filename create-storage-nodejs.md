To access your data in InCountry Platform by using Node.js SDK, you need to create an instance of the `Storage` class using the `createStorage` async factory method.

```typescript
type StorageOptions = {
  apiKey?: string;          // Required when using API key authorization, or as the INC_API_KEY environment variable 
  environmentId?: string;   // Required to be passed in, or as the INC_ENVIRONMENT_ID environment variable 

  oauth?: {
    clientId?: string;      // Required when using oAuth authorization, can be also set through the INC_CLIENT_ID environment variable
    clientSecret?: string;  // Required when using oAuth authorization, can be also set through INC_CLIENT_SECRET environment variable
    authEndpoints?: {       // Custom endpoints regional map to use for fetching oAuth tokens
      default: string;
      [key: string]: string;
    };
  };

  endpoint?: string;        // Defines API URL
  encrypt?: boolean;        // If false, encryption is not used. Defaults to true.

  logger?: Logger;
  getSecrets?: Function;    // Used to fetch an encryption secret
  normalizeKeys?: boolean;
  countriesCache?: CountriesCache;
  hashSearchKeys?: boolean; // Set to false to enable partial match search among record's text fields `key1, key2, ..., key10`. Defaults to true.

  /**
   * Defines API base hostname part to use.
   * If set, all requests will be sent to https://${country}${endpointMask} host instead of the default
   * one (https://${country}-mt-01.api.incountry.io)
   */
  endpointMask?: string;

  /**
   * If your PoPAPI configuration relies on a custom PoPAPI server (rather than the default one)
   * use the `countriesEndpoint` option to specify the endpoint responsible for fetching the list of supported countries.
   */
  countriesEndpoint?: string;

  httpOptions?: {
    timeout?: NonNegativeInt; // Timeout in milliseconds.
  };
};

async function createStorage(
  options: StorageOptions,
  customEncryptionConfigs?: CustomEncryptionConfig[]
): Promise<Storage> {
  /* ... */
}

const { createStorage } = require('incountry');
const storage = await createStorage({
  apiKey: 'API_KEY',
  environmentId: 'ENVIRONMENT_ID',
  oauth: {
    clientId: '',
    clientSecret: '',
    authEndpoints: {
      default: 'https://auth',
    },
  },
  endpoint: 'INC_URL',
  encrypt: true,
  getSecrets: () => '',
  endpointMask: '',
  countriesEndpoint: '',
  httpOptions: {
    timeout: 5000,
  },
});
```

---
**WARNING**

API Key authorization is being deprecated. The backward compatibility is preserved for the `apiKey` parameter but you no longer can access API keys (neither old nor new) from your dashboard.

---

The `oauth.clientId`, `oauth.clientSecret` and `environmentId` variables can be fetched from your dashboard on InCountry Portal.


Otherwise you can create an instance of the `Storage` class and run all asynchronous checks by yourself (or skip their running at your own risk!).

```typescript
const { Storage } = require('incountry');
const storage = new Storage({
  apiKey: 'API_KEY',
  environmentId: 'ENVIRONMENT_ID',
  endpoint: 'INC_URL',
  encrypt: true,
  getSecrets: () => '',
});

await storage.validate();
```

The `validate` method fetches the secret using `GetSecretsCallback` and validates it. If custom encryption configurations were provided they would also be checked with all the matching secrets.


#### oAuth Authentication

The SDK also supports oAuth authentication credentials instead of plain API key authorization. oAuth authentication flow is mutually exclusive with API key authentication - you will need to provide either API key or oAuth credentials.

Below you can find the example of how to  create a storage instance with oAuth credentials (and also provide a custom oAuth endpoint):
```typescript
const { Storage } = require('incountry');
const storage = new Storage({
  environmentId: 'ENVIRONMENT_ID',
  endpoint: 'INC_URL',
  encrypt: true,
  getSecrets: () => '',
  oauth: {
    clientId: 'CLIENT_ID',
    clientSecret: 'CLIENT_SECRET',
    authEndpoints: {
      "default": "https://auth-server-default.com",
      "emea": "https://auth-server-emea.com",
      "apac": "https://auth-server-apac.com",
      "amer": "https://auth-server-amer.com",
    },
  },
});
```


#### Encryption key/secret

The `GetSecretsCallback` function is used to pass a key or secret used for encryption.

Note: even though SDK uses PBKDF2 to generate a cryptographically strong encryption key, you must ensure that you provide a secret/password which follows the modern security best practices and standards.

The `GetSecretsCallback` function returns either a string representing your secret or an object (we call it `SecretsData`) or a `Promise` which is resolved to that string or object:

```typescript
type SecretOrKey = {
  secret: string;
  version: NonNegativeInt;
  isKey?: boolean;
  isForCustomEncryption?: boolean;
};

type SecretsData = {
  currentVersion: NonNegativeInt;
  secrets: Array<SecretOrKey>;
};

/// SecretsData example
{
  secrets: [
    {
      secret: 'aaa',
      version: 0
    },
    {
      secret: 'base64...IHN0cmluZw==', // Should be a base64-encoded key (32 byte key)
      version: 1,
      isKey: true
    },
    {
      secret: 'ccc',
      version: 2,
      isForCustomEncryption: true
    }
  ],
  currentVersion: 1
};
```

The `GetSecretsCallback` function allows you to specify multiple keys/secrets which the SDK will use for decryption based on the version of the key or secret used for encryption. Meanwhile SDK will encrypt data only by using a key/secret pair which matches the `currentVersion` parameter provided in the `SecretsData` object.

This enables the flexibility required to support Key Rotation policies when secret/key pairs must be changed with time. The SDK will encrypt data by using the current secret/key pair while maintaining the ability to decrypt data records that were encrypted with old key/secret pairs. The SDK also provides a method for data migration which allows to re-encrypt data with the newest key/secret. For details please see the `migrate` method.

The SDK allows you to use custom encryption keys, instead of secrets. Please note that a user-defined encryption key should be a 32-character 'utf8' encoded string as required by AES-256 cryptographic algorithm.

Below you can find several examples of how you can use the `GetSecretsCallback` function.

```typescript
type GetSecretsCallback = () => string | SecretsData | Promise<string> | Promise<SecretsData>;

// Synchronous
const getSecretsSync = () => 'longAndStrongPassword';

// Asynchronous
const getSecretsAsync = async () => {
  const secretsData = await getSecretsDataFromSomewhere();
  return secretsData;
};

// Using promises syntax
const getSecretsPromise = () =>
  new Promise(resolve => {
    getPasswordFromSomewhere(secretsData => {
      resolve(secretsData);
    });
  });
```